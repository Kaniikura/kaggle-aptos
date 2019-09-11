順位 1209th/2944

/py にコードがあります(リファクタリングはしていない)  
/inputおよび/dataは容量の関係で中身空です

### 反省
* train，publicのcvが大きく違うコンペだった．(local 0.9+に対してpublicLB 0.78ぐらい)．
* ラベラーが一人であることも考慮して，基本的にノイズまみれのデータでありpublicLBは当てにならないという方針をコンペ初期から取っていた．
* 2015のコンペのデータでpretrainして，2019に対しては5~7epochと少なめに訓練することで
  ロバストなモデルを構築した．
* 結果としてこの方針は裏目にでた．**privateLBはtrainと非常に強い相関**があった．
* おそらくpublicLBのスコアが低かったのはクラスの分布が実データ(病状がsevereなデータほど少ない)とかけ離れており，
  モデルの性能がうまく発揮できなかったためと思われる．(基本的にラベル0の予測は得意で，severeなラベルは苦手)．
  一方，privateはtrainとほぼ同じラベル分布だった．
* In this competition, "Trust local cv" is exactly a maxim.
* Pytorchに触れることもできたし，色々と学びが多かった．いつか別の画像クラス分類コンペでリベンジをしたい．

↓以下，便所の落書き
* Diabetic Retinopathyは人間にとっても難しいタスクなので，ラベラーが１人というコンペ設計がそもそも疑問.
* qwk0.93+は高すぎ．先行研究([これ](https://ai.googleblog.com/2018/12/improving-effectiveness-of-diabetic.html)とか[これ](https://twitter.com/_gregschmidt/status/873072759193993217))を見たらいかに難しい問題なのかがわかる.
* ラベラーが大量のデータをひと目でラベル付けしているとしたら，モデルの学習は簡単そうだし，それで良いスコアが出たとしても，
  結局ラベラーの癖を学習しているでけなのでは？
* ~実データに適応した解法を模索するためのコンペとしては失敗とおもった．~
### その他
#### 前処理
前景を円だと仮定すれば，単純な数学の計算で背景の割合から拡大率を算出できる．  
testデータでは前処理済み(crop&zoom)のデータが多かったため，それに近づけるようにtrainと2015データを前処理した．  
augmentationは水平・垂直フリップと±5°回転． 
#### モデル
EfficientNetb4  
回帰(loss: MSE)とクラス分類(loss: FocalLossのデータ数による重み付け)の両方で学習し，アンサンブルした．
### うまくいかなかったもの
* [ロバストな誤差関数](https://github.com/jonbarron/robust_loss_pytorch/blob/master/example.ipynb)
* Metric learning
* オブジェクトの形状を一致させるような前処理([参考](https://www.kaggle.com/fhopfmueller/removing-unwanted-correlations-in-training-public))

