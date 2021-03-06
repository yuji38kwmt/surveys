FCOS: Fully Convolutional One-Stage Object Detection
===

Zhi Tian, Chunhua Shen, Hao Chen, Tong He

[paper](https://arxiv.org/abs/1904.01355v3)

@cohama


## 補足説明
Anchor (アンカー) とは
[いい感じの説明 (p47)](https://www.slideshare.net/minorunatsutani/road-damge-ai)

## どんなもの?

- Anchor-Free な Single Shot Object Detection
  - Anchor-Free はアンカーの設計が不要くらいの意味
- RetinaNet と同じ構造、ハイパーパラメータで MS COCO 42.1% (+1.3)
  - 比較のために合わせただけでちゃんと探索するともっと良くなるかもよ
- メモリ量は半分で済む
  - (これは GPU の VRAM のことを言っている？)

## 技術や手法の肝は？

- 構造は RetinaNet と同じ
  - Feature Pyramid あり
  - Feature Pyraid と物体の対応付は設定する必要あり (論文中では stride の 8倍)
  - Focal Loss
- 枠内にあるものは全部正事例として扱う
  - 正事例かどうかを計算しなくても良い
  - かぶりがあったらとりあえず、小さい方
- 検出部分は各ピクセルから上端、左端、下端、右端までの距離を `exp(s * 入力画像の座標)` で回帰
  - s は学習させる
  - 回帰の Loss は IOU Loss
- さらに、centerness という値も出力させる
  - 枠の端では0、ちょうど中心で1になるような実数値
  - 2値問題 (Binary Cross Entoropy) で学習させる
  - 最後の確信度を求める際に Score と掛け算

## どうやって有効だと検証した？

- RetinaNet と同じパラメータを使って実験
  - COCO の mAP
- Overlapping に対する強さについて、COCO から overlap が必ずある画像を集めて評価
  - アンカーなしにもかかわらず overlap に対しても処理できている


## 先行研究と比べて何がすごい？

- Anchor-base な手法はパラメータ探索が大変
  - 実際 RetinaNet も最適なアンカーを探すためにたくさん実験している
- CornerNet は複雑な後処理が必要
- UnitBox、DenseBox は物体のオーバーラップをうまく扱えない


## 議論はある?

- centerness ないと弱い。
  - 中心を教えるのが単純に強い説
- Anchor Free といっているが、要は特殊なアンカーが1個あるだけでは


## 次に読むべき論文
- DenseBox https://arxiv.org/abs/1509.04874
- UnitBox https://arxiv.org/abs/1608.01471
- CornerNet https://arxiv.org/abs/1808.01244
  - 左上と右下の2点のキーポイント検出と、それらが同一物体のペアになるかを推論
- DuBox https://arxiv.org/abs/1904.06883v1
- Feature Selective Anchor-Free Module for Single-Shot Object Detection https://arxiv.org/abs/1903.00621
  - COCO map 44.6%
- Bottom-up Object Detection by Grouping Extreme and Center Points https://arxiv.org/abs/1901.08043
- Objects as Points (CenterNet) https://arxiv.org/abs/1904.07850
- CenterNet (名前がかぶっている...) https://arxiv.org/abs/1904.08189
  - COCO mAP 47%
  - one stage で優勝
