# 東京都Vector Tile

東京都のある一定の範囲で抽出したタイル配信します。

このレポジトリは[東京都新型コロナウイルス感染症対策サイト](https://stopcovid19.metro.tokyo.lg.jp/)([github](https://github.com/tokyo-metropolitan-gov/covid19))を支援するために作成しました。

## 地図のアップデート

tokyo.mbtilesを差し替えてmasterブランチにpushするとGithub Actionsで自動的に公開されます。

また、tile.jsonの絶対パスはGithub Actionsで置換されます。

## mbtiles の作り方

### 事前準備

必要なツールをインストール(Ubuntuの例)

```
sudo apt install bc make osmctools wget git vim docker.io
```

`osmctools`が無い場合は以下の方法で`osmconvert`コマンドを取得する

```
wget -O - http://m.m.i24.cc/osmconvert.c | cc -x c - -lz -O3 -o osmconvert
```

Ubuntuでは`docker-compose`は別途インストールを行う。またDockerの実行権限を付与する。

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo usermod -aG docker $USER
```

[openmaptiles](https://github.com/openmaptiles/openmaptiles/blob/master/README.md) のインストールと設定

```
git clone https://github.com/openmaptiles/openmaptiles.git
cd openmaptiles
# edit .env
vim .env # set QUICKSTART_MAX_ZOOM=14
```

### Tokyo regionの抽出と出力範囲の調整

関東をダウンロード

```
make download-geofabrik "area=kanto"
```

`osmconvert`コマンドと`Tokyo.poly`を用いて`kanto.osm.pbf`から`tokyo.osm.pbf`を抽出する

```
# create Tokyo region osm file
cd data
wget https://raw.githubusercontent.com/tokyo-metropolitan-gov/tokyo-vector-tile/master/Tokyo.poly
osmconvert kanto.osm.pbf -B=Tokyo.poly -o=tokyo.osm.pbf
```

data/docker-compose-config.ymlのBBOXが関東の範囲なのでTokyo.polyに合わせてBBOXの範囲を小さくする

```
# edit docker-compose-config.yml BBOX
vim docker-compose-config.yml # ex. BBOX: " 139.31, 35.49, 139.95, 35.86"
```

### mbtilesの作製

もとのディレクトリに戻ってdocker containerを削除する(importのゴミを消すため)

```
# drop current docker container
cd ..
docker-compose down -v
```

必要なファイルが揃ったので、改めて`quickstart.sh`を`tokyo`オプションをつけて実行する

```
# re-run quickstart.sh with 
./quickstart.sh tokyo
```

出力された `data/tiles.mbtiles` を `tokyo.mbtiles` として本レポジトリにアップロードする。

#### 注意点

- `./quickstart.sh` は引数にある範囲の`{region}.osm.pbf`があるかをチェックします。
- `./quickstart.sh` の引数なしで実行するとアルバニアの範囲がダウンロードされてしまうので注意すること。
- `kanto.osm.pbf`が存在してる段階で`./quickstart.sh kanto`を実行しても新しいファイルはダウンロードされないので新しく作る場合は `data` ディレクトリごと削除するのがおすすめです。

## 範囲

[tokyo.geojson](tokyo.geojson) または [Tokyo.poly](Tokyo.poly) を参照。

## Copyright

- [© OpenMapTiles](https://www.openmaptiles.org/)
- [© OpenStreetMap contributors](https://www.openstreetmap.org/copyright)
