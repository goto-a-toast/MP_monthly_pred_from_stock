# MP Monthly Prediction Model
## 月別係数を用いた翌月限界利益（MP）予測モデル

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/goto-a-toast/MP_monthly_pred_from_stock/blob/main/mp_monthly_prediction_model_v4.ipynb)

## 概要

このプロジェクトは、過去の限界利益（Marginal Profit: MP）データを基に、翌月のMPを高精度で予測する機械学習モデルです。ランダムフォレスト回帰と月別転換率を組み合わせることで、実用的な予測精度を実現しています。

### 主な特徴

- **高精度予測**: R2スコア 0.964、平均絶対誤差（MAE）約204万円（10K JPY単位）
- **ハイブリッドアプローチ**: ランダムフォレストと統計的転換率の組み合わせ
- **月別最適化**: 各月の特性を考慮した予測モデル
- **視覚化機能**: 予測結果をわかりやすいグラフで表示

## モデルの仕組み

このモデルは、翌月のMPを2つの要素に分解して予測します：

### 1. 確定MP（Confirmed MP）
すでに確定している来月の限界利益です。
- **確定MP（Kakutei_MP）**: 前月までに確定している来月のMP
- **AMP**: 来月に増加することがほぼ確定しているMP

### 2. 追加MP（Additional MP）
まだ確定していないが、過去の傾向から予測される追加の限界利益です。

このモデルでは、以下の2つの手法を組み合わせて追加MPを予測します：

#### a. ランダムフォレスト予測（70%の重み）
- BMP、CMP、DMPの各種限界利益データと対象月を特徴量として使用
- 100本の決定木による ensemble 学習
- 非線形な関係性を捉えることが可能

#### b. 月別転換率予測（30%の重み）
- BCD合計（BMP + CMP + DMP）から実際のMPへの転換率を月別に算出
- 過去の統計データに基づいた堅実な予測

**最終予測** = 確定MP + 追加MP

## 必要なライブラリ

```python
pandas
numpy
matplotlib
seaborn
scikit-learn
```

Google Colabで実行する場合、これらのライブラリはプリインストールされています。

ローカル環境で実行する場合：

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

## データフォーマット

入力CSVファイルは以下の列を含む必要があります：

| 列名 | 説明 |
|------|------|
| 行ラベル | 日付（YYYY/MM/DD形式） |
| 合計 / MP | 当月の限界利益 |
| 合計 / 次月確定MP | 次月の確定MP |
| 合計 / 次月AMP | 次月のAMP |
| 合計 / 次月BMP | 次月のBMP |
| 合計 / 次月CMP | 次月のCMP |
| 合計 / 次月DMP | 次月のDMP |

### サンプルデータ

```csv
行ラベル,合計 / MP,合計 / 次月確定MP,合計 / 次月AMP,合計 / 次月BMP,合計 / 次月CMP,合計 / 次月DMP
2020/04/01,52612321,0,15815626,1787889,2610055,58101088
2020/04/02,56827598,0,16514617,2656829,3009163,54178242
```

## 使用方法

### Google Colabでの実行（推奨）

1. 上部の「Open in Colab」バッジをクリック
2. ノートブックが開いたら、「ランタイム」→「すべてのセルを実行」
3. データアップロード画面が表示されたら、CSVファイルをアップロード
4. 自動的に分析と予測が実行されます

### ローカル環境での実行

1. リポジトリをクローン
```bash
git clone https://github.com/goto-a-toast/MP_monthly_pred_from_stock.git
cd MP_monthly_pred_from_stock
```

2. Jupyter Notebookを起動
```bash
jupyter notebook mp_monthly_prediction_model_v4.ipynb
```

3. セル6を修正してローカルファイルを指定
```python
filename = 'your_data.csv'  # あなたのCSVファイルパス
```

4. すべてのセルを実行

## 予測の実行

### 最新データでの予測

ノートブックのセル20を実行すると、最新の月末データを使用して翌月のMPを予測します。

### カスタム予測

セル21で任意のパラメータを入力して予測できます：

```python
p = predictor.predict(
    kakutei=609289,      # 確定MP
    amp=47905097,        # AMP
    bmp=5236622,         # BMP
    cmp=3481478,         # CMP
    dmp=18589534,        # DMP
    source_month=1       # 予測元の月（1=1月末→2月予測）
)
```

## 出力結果

モデルは以下の情報を提供します：

- **月別転換率**: 各月のBCDからMPへの転換率統計
- **予測精度**: MAE（平均絶対誤差）とR2スコア
- **月次予測**: 確定MP、追加MP、最終予測値
- **視覚化**:
  - 月別転換率の棒グラフ
  - 月別予測シミュレーション
  - 実績値 vs 予測値の時系列比較

## モデル性能

訓練データ（2020年4月〜2026年1月、69ヶ月分）での性能：

- **R2スコア**: 0.964（非常に高い説明力）
- **MAE**: 約204万円（10K JPY単位）

## プロジェクト構成

```
MP_monthly_pred_from_stock/
├── mp_monthly_prediction_model_v4.ipynb  # メインノートブック
└── README.md                              # このファイル
```

## 注意事項

- データの品質が予測精度に大きく影響します
- 月末時点のデータを使用することを推奨します
- 総計行などの非日付データは自動的に除外されます

## ライセンス

このプロジェクトはオープンソースです。

## 貢献

バグ報告や機能リクエストは、GitHubのIssuesページでお願いします。

## 作者

goto-a-toast

## 更新履歴

- **v4**: ランダムフォレストと月別転換率のハイブリッドモデル実装
- 視覚化機能の強化
- コードの最適化とバグ修正
