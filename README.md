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
| `exp011` | `swin_large_patch4_window7_224` | binary | 224 | - |

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
