# 東京都Vector Tile

東京都のある一定の範囲で抽出したタイル配信します。

## 地図のアップデート

tokyo.mbtilesを差し替えてmasterブランチにpushするとGithub Actionsで自動的に公開されます。

また、tile.jsonの絶対パスはGithub Actionsで置換されます。

## mbtiles の作り方

### 事前準備

[openmaptiles](https://github.com/openmaptiles/openmaptiles/blob/master/README.md) のインストール

```
git clone https://github.com/openmaptiles/openmaptiles.git
cd openmaptiles
# Build the imposm mapping, the tm2source project and collect all SQL scripts
make
./quickstart.sh
```

[osmtools](https://gitlab.com/osm-c-tools/osmctools) のインストール

```
wget -O - http://m.m.i24.cc/osmconvert.c | cc -x c - -lz -O3 -o osmconvert
```

### kanto データの processing

`何かを実行`

kanto のダウンロードが終わったあとCtrl+Cで止める

osmconvertで東京のだいたい都心部が入ったPBFを作成

```
osmconvert -B=Tokyo.poly -o tokyo.pbf ???
```

生成されたdocker-compose-config.ymlを書き換える

```
何を？
```

`tokyo.mbtiles` が完成する

`./quickstart.sh tokyo`



## 範囲

Tokyo.poly を参照。

