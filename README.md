# PetFinder Pawpularity Contest
## experiments
| exp_id | model | task | img_size | CV | Public | Memo |
| --- | --- | --- | --- | --- | --- | --- |
| `exp001` | `swin_tiny_patch4_window7_224` | regression | 224 | - | 18.62484
| `exp002` | `swin_tiny_patch4_window7_224` | binary | 224 | - | 18.44126
| `exp003` | `swin_base_patch4_window7_224` | binary | 224 | - | - |
| `exp004` | `swin_base_patch4_window7_224` | binary | 224 | - | 18.23485
| `exp005` | `swin_large_patch4_window7_224` | binary | 224 | - | 18.09551
| `exp006` | `swin_large_patch4_window7_224_in22k` | binary | 224 | - | 18.10436
| `exp007` | `swin_large_patch4_window12_384` | binary | 384 | - | 18.05763
| `exp008` | `swin_large_patch4_window12_384` | binary | 384 | - | 17.96711 (17.96861) | (TTA)
| `exp009` | `swin_large_patch4_window12_384_in22k` | binary | 384 | - | 17.96142
| `exp010` | `swin_large_patch4_window12_384_in22k` | binary | 384 | - | 18.04634
| `exp011` | `swin_large_patch4_window7_224` | binary | 224 | - | 18.14715
| `exp012` | `swin_large_patch4_window7_224` | binary | 224 | - | 18.12262
| `exp013` | `vit_large_patch32_384` | binary | 384 | - | 18.42981
| `exp014` | `vit_large_patch16_224` | binary | 224 | - | 18.17882
| `exp015` | `swin_large_patch4_window12_384_in22k` | binary | 384 | 17.832 | 17.91612(17.88841)
| `exp017` | `swin_large_patch4_window12_384_in22k` | binary | 384 | - | 18.02289(17.98649)
| `exp018` | `swin_large_patch4_window12_384_in22k` | binary | 384 | 16.105 | 18.29305 | target=100を除去
| `exp019` | `swin_large_patch4_window12_384_in22k` | binary | 384 | - | 18.03601 | 2値分類 + 外れ値除去モデル
| `exp020` | `swin_large_patch4_window12_384_in22k` | binary | 384 | 17.636 | 17.99507(17.95575)
| `exp021` | `tf_efficientnet_b4` | binary | 380 | 18.715 | -
| `exp022` | `swin_large_patch4_window12_384_in22k` | binary | 384 | 18.463 | -
| `exp023` | `swin_large_patch4_window12_384_in22k` | binary | 384 | 17.768 | 17.93922(17.90762)

## kaggle blog
### 2021-12-05
- `exp009`からkaggle日記を始めた
- 序盤のepochではaugmentationを弱めにして徐々に強めていきたい
    - alubumentationのスケジューラのrepoを見つけたのでそれを使ってみたい
- `exp009`
    - fold数を5から10に変更
    - テーブル特徴も含めたmixup
- `exp010`
    - 効率化のためにfold数を5に戻した。
    - スケジューラをwarmupにして徐々に上がるようにした
### 2021-12-06
- Cassavaコンペの解法を参考にしてみてもいいかもしれない
    - シンプルなaugmentation
    - Multi drop linear
    - LR(Lambda LR)
- Discussionを除いてみると、swinは384よりも224の方がパフォーマンスが良いらしい
    - 現状自分のモデルは384の方がLBがいい(テーブル特徴を加えたからかも)
    - 何故だろう？基本的には画像サイズは大きい方がいいというイメージがあるが・・・
- `exp011`
    - Cassavaコンペを参考
    - augmentationを変更
        - Transpose, Horizontal, Vertial, ShiftedRotateResizeのみに
    - LRを徐々に下げていくスケジューラに変更
    - HeadのDropoutを復活
- `exp012`
    - MultiSampleDropoutを追加
        - 実際はnn.Dropoutレイヤーのみ異なるので実装が違っている・・・
### 2021-12-07
- `exp013`
- `exp014`
    - ViTを試してみる
- MixUpについて
    - 回帰タスクでは相性が悪いかも？(atmaCup#11)
    - 重み付けラベルに意味があるかどうか(10と50を混ぜたら30の画像になるのか？)
### 2021-12-08
- `exp015`
    - `exp009`をベースに改良
    - LBは自己最高にになった。(TTAを適用すると更に伸びたので効いている？)
    - vallossの推移からepoch数は少なめでいい？(trainにfitさせすぎないのが肝？)
    - Augmentationを変更した
        - ResizeCropする前にFlip入れたり
        - CutOutの追加
    - mixupを外した
        - mixupなしの方が良かったか？(厳密な検証ではない)
### 2021-12-10
- `exp015`のTTAを3回から5回に増やしてsub
- エラー分析ができるようにログを出力したい
### 2021-12-11
- `exp016`
    - マルチタスク学習
    - BCE : RMSE = 7 : 3でlossを算出
- `exp017`
    - `exp015`とほぼ同じだが画像特徴とconcatする前にMLPでembedding
### 2021-12-12
- kaggleのRMSEコンペを調査
    - ELOコンペ
        - RMSEは外れ値の影響を受けやすいのでまず外れ値がどうかの二値分類モデルを学習するアプローチがあった
        - 更にその確率と外れ値なしで学習したモデルの予測値で加重平均するなど
- `exp018`
    - 外れ値(100)かどうかを予測するタスク
- `exp019`
    - 外れ値を除外したデータで学習
### 2021-12-13
- `exp020`
    - `exp015`の10Foldバージョン
    - PublicLBは上がってしまった
### 2021-12-15
- `exp021`
    - backboneをEfficientNetB4に変更
    - LocalCVが上がってしまった。swin系が強いのか？
### 2021-12-16
- discussionに上がっていたbatch sizeとlrのスレッドを見てチューニングをやってみた
- `exp022`
    - `exp015`ベース
    - lrを1.5e-5 -> 5e-5に変更
- `exp023`
    - `exp015`ベース
    - batch sizeを倍にしたかったがモデルが重いためgrad accumulateを用いてbatch size=8にした
### 2021-12-17
- kaggleのcassavaコンペを参考にしているが、ラベルがNoisyな点などが似ているためshake up/downが予想される
- shakeを耐えるためには個々のモデルの精度を突き詰めるより多様なモデルを用意してアンサンブルする方向にシフトした方が良さそう
- cassavaだと各モデルの予測値の平均をとるだけでいいスコアっぽいので残り時間考えてその方針で進みたい
- それ以外にNoisy Labelにロバストなlossがあったりするのでそれを試す
- `exp024`
    - lossをBi-Tempered Logistic Lossに変更
    - 外れ値のロスの影響を減らす+softmaxの裾の幅広げることでラベルノイズの効果弱めるLogistic Lossらしい
    - cassavaコンペで使われていた
