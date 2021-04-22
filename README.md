# kaggle-indoor
Indoor Location and Naviagtion (https://www.kaggle.com/c/indoor-location-navigation) 用のリポジトリ  

- Directory tree
```
kaggle-indoor
├── README.md
├── data         <---- データを置くかもな場所
└── notebook     <---- colabで作ったnotebook

```

# About this competiion
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
|  indoor-unified-wifi-ds  |  koukiさんの作った神データセット たぶんWiFiのみについて集計がしてある  |  |


# Notebook
| Notebook  |  Description  | Ref |
| ---- | ---- | ---- |
|  EXP001  |  koukiさんの作ったデータセットとnotebook + Cost Minimization のBaseline  |indoor-unified-wifi-ds |
|  EXP002  |  EXP001でseqlenを5にしてtrainしたやつ + Cost Minimization   |indoor-unified-wifi-ds |

# Logs
## 20210315
- colabでkaggleやる環境だけ整備した
- データの前処理がしんどすぎそうなので、とりあえずkoukiさんのデータセット(indoor-unified-wifi-ds)使ってなんとなくの様子をみたいなという感じ
- indoor-unified-wifi-dsは色々データあるなかで、wifiの強度に関して集計しれくれたやつっぽい
- あるユーザーがあるwaypointにいるとき、WiFiの強度(rrsi？みたいなやつ)が近い順にcolumnsに並べて、そのユニークなidと強度がどんくらいかを示している あとfloorとかも乗ってる
- 次回は実際にkoukiさんdataset & notebook を見ながらハンズオン的な感じでとりあえずデータ見る＆動かしてみる ってことをしたい

## 20120320
- koukiさんデータセット読み込んで、前処理とネットワーク写した
- BSSIDとsite_idをembedd、RSIIをLinearかけて、それら全部を横に結合して、さらにLinearかけてLSTMに入れてるっぽい
- LSTMは(1, batch_size, num_features)をinputにしてる
- 真ん中のとこが時系列の要素になってるっぽい(多分........)
- BSSIDの時系列の変化を見ることで、暗黙的にpathごとの変化を学習してる的な感じなのかなと思った
- でもpathごとにミニバッチにして、(new_batch_size, path_batch_size, features)にした方がよりpathに沿った時系列の変化を学習できるのではという気がする
- 一回元のコード通りに書いてやってみたあと、上のやつを試してみたいなあとは思う
- 次回は学習、評価までやってみたい あとディスカッションにあった神postprocessingも試したい

## 20210321
- koukiさんnotebooksからmodelingの関数とtraining stepを拝借した
- そしたらtrainingのときにOOMっぽいエラーになった GPUではなく普通のRAMのほう
- colab proにしたらRAMが2倍くらいになるらしいからそれを試してみようかな
- でも普通に考えてRAMのほうのメモリが増大してくのは何かtrainingの書き方に何かしらか問題がある気がする
- epochsごとに無限にデータフレーム作っちゃってるとかデータを格納してるdictの中身が無限に増えてくとか、そういう感じ＞
- 次回そこを検証してとりあえずちゃんと学習を終わらせられるようにしたい

## 20210331
- いろいろメモリ効率とか学習速度とか改善した
- 5foldで15epochsくらいにしたら結構すぐ学習終わるようになった 定時後に回して深夜におわるレベル
- kouki-sanのやつ + Post-processing by Cost Minimization (https://www.kaggle.com/saitodevel01/indoor-post-processing-by-cost-minimization) をとりあえず完コピした
- CV?的なmean position error：192.2107121781046 で LB 5.986 だった
- baselineができたので次はpathごとにミニバッチにするとかいろいろしたい
- なおまだ理解ちゃんとできてない箇所も多いからとりあえずコピったとこはちゃんと理解するとこから始めたい

## 20210422
- EXP001はseqlenが1だったからデータセットの渡し方を変えてseqlen==5にしてtrainingした post processもした
- lossは前より下がってていい感じと思ったけどLBは6.008でなんか悪くなってた
- seqlen5程度で悪くなるとかある？？なんか評価用の関数とか渡してる評価値とか間違ってるのでは
- というか今はfloorを99 accurateみたいなやつの値を使ってるから適切なcvが出せてない気がする
- そこを含めて学習中に適切なCVが見れるように変えたいなと思いました
- seqlenを増やすことでlossが下がってfloor含むmseが悪くなったのが気になる
- 評価関数でfloorとseqlenの関係を調べてみる
- 評価関数:np.sqrt((xhat-x)**2 + (yhat-y)**2) + 15 * np.abs(fhat-f)
- 一概には言い切れないけど、lossが下がると左のsqrtのとこは小さくなるはず まぁoverfittingしてるかもですが…
- 一度に評価するバッチサイズが大きくなると、floorが適当な値だからペナルティ項の影響がどんどん大きくなる たぶん
- 評価の値がバッチサイズとかseqlenの影響受けるのあほらしいから用意されたfloor使うんならそれを最初からデータに食わせておくか、floorもちゃんと学習するモデルにするかをしたい
- それは置いといて、ちゃんと学習できてると仮定するとスコアは上がってるはずでは？
- いや、ただseqは1ずらしで与えてるから、評価するときは最初のindexの値のみを与えるべきでは？
- 切れ目のとこを与えたくないからseqlenを5にしてるのに、1ずらしにすることで切れ目のとこも結構データとして食わせちゃってるということになるかもしれない
- これは重複なしで与えるべきかな
- それか、でかいseqlenを与えて、pathが途切れたときはpaddingするようにしたほうがいいかもしれない
- ということで、次回は色々な関数の見直し、ちゃんとCV見れるようにする、重複無しでデータセットつくる、paddingを試す、などをやりたいと思います
