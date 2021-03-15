# kaggle-indoor
Indoor Location and Naviagtion(https://www.kaggle.com/c/indoor-location-navigation)用のリポジトリ  

- Directory tree
```
kaggle-indoor
├── README.md
├── data         <---- データを置くかもな場所
└── notebook     <---- colabで作ったnotebook

```

# About this competision
## Introduction
Your smartphone goes everywhere with you—whether driving to the grocery store or shopping for holiday gifts. With your permission, apps can use your location to provide contextual information. You might get driving directions, find a store, or receive alerts for nearby promotions. These handy features are enabled by GPS, which requires outdoor exposure for the best accuracy. Yet, there are many times when you’re inside large structures, such as a shopping mall or event center. Accurate indoor positioning, based on public sensors and user permission, allows for a great location-based experience even when you aren’t outside.

Current positioning solutions have poor accuracy, particularly in multi-level buildings, or generalize poorly to small datasets. Additionally, GPS was built for a time before smartphones. Today’s use cases often require more granularity than is typically available indoors.

In this competition, your task is to predict the indoor position of smartphones based on real-time sensor data, provided by indoor positioning technology company XYZ10 in partnership with Microsoft Research. You'll locate devices using “active” localization data, which is made available with the cooperation of the user. Unlike passive localization methods (e.g. radar, camera), the data provided for this competition requires explicit user permission. You'll work with a dataset of nearly 30,000 traces from over 200 buildings.

If successful, you’ll contribute to research with broad-reaching possibilities, including industries like manufacturing, retail, and autonomous devices. With more accurate positioning, existing location-based apps could even be improved. Perhaps you’ll even see the benefits yourself the next time you hit the mall.

## Raw Data
TBD.

# Dataset
| Dataset  |  Description  | Ref |
| ---- | ---- | ---- |
|  indoor-unified-wifi-ds  |  koukiさんの作った神データセット たぶんWiFiのみについて集計がしてある  | |


# Logs
## 20210315
- colabでkaggleやる環境だけ整備した
- データの前処理がしんどすぎそうなので、とりあえずkoukiさんのデータセット(indoor-unified-wifi-ds)使ってなんとなくの様子をみたいなという感じ
- indoor-unified-wifi-dsは色々データあるなかで、wifiの強度に関して集計しれくれたやつっぽい
- あるユーザーがあるwaypointにいるとき、WiFiの強度(rrsi？みたいなやつ)が近い順にcolumnsに並べて、そのユニークなidと強度がどんくらいかを示している あとfloorとかも乗ってる
- 次回は実際にkoukiさんdataset & notebook を見ながらハンズオン的な感じでとりあえずデータ見る＆動かしてみる ってことをしたい
