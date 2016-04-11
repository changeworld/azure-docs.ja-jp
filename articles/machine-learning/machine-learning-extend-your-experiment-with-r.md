<properties
	pageTitle="R を使用した実験の拡張 | Microsoft Azure"
	description="R スクリプトの実行モジュール使用して、R 言語によって Azure Machine Learning Studio 機能を拡張する方法。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye" />


#R を使用した実験の拡張

[R スクリプトの実行][execute-r-script]モジュール使用して、R 言語によって ML Studio 機能を拡張できます。

このモジュールは、複数の入力データセットを受け取り、1 つのデータセットを出力として生成します。[R スクリプトの実行][execute-r-script]モジュールの **R Script** パラメーターに、R スクリプトを入力できます。

モジュールの各入力ポートにアクセスするには、次のようなコードを使用します。

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##現在インストールされているすべてのパッケージを一覧表示する

インストールされているパッケージの一覧を変更できます。完全な一覧を取得するには、次の行を [R スクリプトの実行][execute-r-script]モジュールに含めると、出力データセットに一覧が送信されます。

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

パッケージの一覧を表示するには、[CSV に変換][convert-to-csv]などの変換モジュールを、[R スクリプトの実行][execute-r-script]モジュールの出力に接続し、実験を実行します。次に、変換モジュールの出力をクリックし、**[ダウンロード]** を選択します。便宜上、ここでは [Excel 形式でのバージョン番号を含む現在の完全なリスト](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)です。

##パッケージをインポートする

[R スクリプトの実行][execute-r-script]モジュールと圧縮されたパッケージのアーカイブで次のコマンドを使用して、ステージングされた ML Studio リポジトリから、まだインストールされていないパッケージをインポートすることもできます。

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

ここで、`my_favorite_package.zip` にはパッケージの zip ファイルが含まれています。

##インストール済みパッケージの一覧

参考までに、現在のリリースに含まれるパッケージの一覧を次の表に示します。

現在使用できるパッケージの完全な一覧を取得するには、上記の**現在インストールされているすべてのパッケージを一覧表示する**のセクションをご覧ください。R に関する最新のドキュメントは、[こちら](http://cran.r-project.org/manuals.html)から入手できます。

- [A ～ E で始まる R モジュール]
- [F ～ L で始まる R モジュール]
- [M ～ R で始まる R モジュール]
- [S ～ Z で始まる R モジュール]

[A ～ E で始まる R モジュール]: #r-modules-beginning-with-a-through-e
[F ～ L で始まる R モジュール]: #r-modules-beginning-with-f-through-l
[M ～ R で始まる R モジュール]: #r-modules-beginning-with-m-through-r
[S ～ Z で始まる R モジュール]: #r-modules-beginning-with-s-through-z


###A ～ E で始まる R モジュール

| パッケージ名 | パッケージの説明 |
| ------------ | ------------------- |
| abc | 近似ベイズ計算のためのツール (ABC) |
| abind | 多次元配列を結合する |
| actuar | 数理計算上の関数 |
| ade4 | 生態学的データの分析: 探索と環境科学におけるユークリッド メソッド |
| AdMit | スチューデント t 分布の適応混合 |
| aod | Overdispersed データの解析 |
| ape | 系統学と進化の分析 |
| approximator | 複雑なコンピューター コードのベイズ予測 |
| arm | 回帰とマルチレベル / 階層モデルを用いたデータ分析 |
| arules | 相関ルールと高頻度項目セットのマイニング |
| arulesViz | 相関ルールと高頻度項目セットの可視化 |
| ash | David Scott の ASH ルーチン |
| assertthat | 簡単な前処理および後処理のアサーション |
| AtelieR | 統計的推論で基本的な概念を教え、基本ベイズ テストを行うための GTK の GUI |
| BaBooN | ベイズ ブートストラップ予測平均マッチング – 離散データに対する複数の単一代入 |
| BACCO | コンピュータ コード出力のベイズ分析 (BACCO) |
| BaM | Jeff Gil の書籍のための関数とデータセット |
| bark | ベイズ加法回帰カーネル |
| BAS | ベイズ アダプティブ サンプリングを使用したベイズ モデル平均化 |
| base | R 基本パッケージ |
| BayesDA | 書籍「ベイズ データ解析」のための関数とデータセット |
| bayesGARCH | スチューデント T イノベーションと GARCH(1,1) モデルのベイズ推定 |
| bayesm | マーケティング / マイクロ計量経済学のためのベイズ推論 |
| bayesmix | JAGS 使用したベイズ混合モデル |
| bayesQR | ベイズ分位回帰 |
| bayesSurv | 柔軟なエラーとランダム効果分布とベイズ生存回帰 |
| Bayesthresh | カテゴリ データのためのベイズしきい値混合効果モデル |
| BayesTree | ツリー ベース モデルのベイズ法 |
| BayesValidate | BayesValidate パッケージ |
| BayesX | ソフトウェア パッケージ BayesX に付属の R ユーティリティ |
| BayHaz | ベイズ ハザード率推定のための R 関数 |
| bbemkr | ガウス誤差と多変量カーネル回帰のベイズ帯域推定 |
| BCBCSF | 選択された特徴を持つバイアス補正後のベイジアン分類 |
| BCE | ベイズ組成推定: バイオマーカー データからのサンプル (分類学) 組成の推定 |
| bclust | 高次元データをクラスタリングするのに適したスパイク·アンド·スラブ階層モデルを用いたベイズ クラスタリング |
| bcp | 変更ポイントの問題のベイズ分析を実施するためのパッケージ |
| BenfordTests | ベンフォードの法則に適合を評価するための統計的検定 |
| bfp | ベイズ分数多項式 |
| BH | ブースト C++ ヘッダー ファイル |
| bisoreg | バーンスタイン多項式とベイズ等張回帰 |
| bit | 1 ビットのブール値のベクトルのクラス |
| bitops | ビット演算 |
| BLR | ベイジアン線形回帰 |
| BMA | ベイズ モデル平均化 |
| Bmix | 棒折り過程の混合のためのベイズ サンプリング |
| BMS | ベイズ モデル平均化ライブラリ |
| bnlearn | ベイジアン ネットワークの構造学習、パラメータ学習と推論 |
| boa | MCMC のためのベイジアン出力解析プログラム (BOA) |
| Bolstad | Bolstad 関数 |
| boot | ブートストラップ関数 (Angelo Canty による、元は S 向け） |
| bootstrap | 書籍「ブートストラップの概要」のための関数 |
| bqtl | ベイズ QTL マッピング ツールキット |
| BradleyTerry2 | Bradley Terry モデル |
| brew | レポート生成のためのテンプレート フレームワーク |
| brglm | 二項応答一般化線形モデルにおけるバイアス削減 |
| bspec | ベイジアン スペクトル推定 |
| bspmma | bspmma: メタ分析のためのベイズ セミパラメトリック モデル |
| BVS | ベイズ バリアント選択: 遺伝的関連研究のためのベイズ モデルの不確実性のテクニック |
| cairoDevice | カイロベースのクロスプラットフォームのアンチエイリアス化グラフィックス·デバイス·ドライバ |
| calibrator | 複雑なコンピュータコードのベイジアン キャリブレーション |
| car | 適用された回帰の関連ドキュメント |
| caret | 分類と回帰のトレーニング |
| catnet | カテゴリ型ベイジアン ネットワーク推論 |
| caTools | ツール: 移動ウィンドウ統計、GIF、Base64、ROC AUC など |
| chron | 日付と時刻を処理できる時系列オブジェクト |
| class | 分類関数 |
| cluster | クラスター分析拡張 Rousseeuw など |
| clusterSim | データ·セットのための最適なクラスタリング手順の検索 |
| coda | MCMC のための出力解析と診断 |
| codetools | R 用のコード分析ツール |
| coin | 順列テスト フレームワークの条件推論手続き |
| colorspace | 色空間の操作 |
| combinat | 組み合わせ論ユーティリティ |
| compiler | R コンパイラ パッケージ |
| corpcor | 共分散の効率的な推定と (部分) の相関 |
| cslogistic | 条件付きで指定されたロジスティック回帰 |
| ctv | CRAN タスク ビュー |
| cubature | ハイパーキューブ上の適応多変数の統合 |
| data.table | Data.frame の拡張機能 |
| datasets | R データセット パッケージ |
| date | 日付を処理するための関数 |
| dclone | 最尤法のデータ クローニングと MCMC ツール |
| deal | 混合変数を使用したベイジアン ネットワークの学習 |
| Deducer | Deducer: Rのデータ解析GUI |
| DeducerExtras | Deducer のための追加のダイアログと関数 |
| deldir | ドロネー三角形分割とディリクレ (ボロノイ) テッセレーション |
| DEoptimR | 純粋な R の微分進化最適化 |
| deSolve | 常微分方程式 (ODE)、偏微分方程式 (PDE)、微分代数方程式 (DAE)、遅延微分方程式の初期値問題に対する一般的なソルバー (DDE) |
| devtools | R コードの開発を容易にするツール |
| dichromat | 色覚のためのカラー スキーム |
| digest | R オブジェクトの暗号化ハッシュ ダイジェストを作成する |
| distrom | 分散多項回帰 |
| dlm | 動的線形モデルのベイズと可能性分析 |
| doSNOW | Snow パッケージの Foreach 並列アダプター |
| dplyr | dplyr: データ操作の文法 |
| DPpackage | R のベイズ ノンパラメトリック モデリング |
| dse | 動的なシステムの推定 (時系列パッケージ) |
| e1071 | 統計部門の他の関数 (e1071)、TU Wien |
| EbayesThresh | 経験的ベイズしきい値処理と関連メソッド |
| ebdbNet | 動的ベイジアン ネットワークの実証的ベイズ推定 |
| effects | リニア、一般化線形、多項ロジット、比例オッズ ロジット モデル、混合効果モデルのエフェクトを表示 |
| emulator | コンピューター プログラムのベイジアン エミュレーション |
| ensembleBMA | アンサンブルとベイズ モデルの平均化を使用した確率予測 |
| entropy | エントロピ、相互情報、関連数量の推定 |
| EvalEst | 動的システムの推定 - 拡張機能 |
| evaluate | 既定よりも詳細情報を提供する解析·評価ツール |
| evdbayes | 極値理論のベイジアン解析 |
| evora | リスク予測分析のためのエピジェネティックな変数外れ値 |
| exactLoglinTest | 対数線形モデルのモンテカルロ正確確率検定 |
| expm | 行列指数関数 |
| extremevalues | 単変量外れ値検出 |


###F ～ L で始まる R モジュール

| パッケージ名 | パッケージの説明 |
| ------------ | ------------------- |
| factorQR | ベイズ分位回帰係数モデル |
| faoutlier | 因子分析と構造方程式モデルの影響力のあるケース検出方法 |
| fitdistrplus | 非打ち切りまたは打ち切りデータへのパラメトリック分布の適合に役立つ |
| FME | 逆モデリング、感度、識別可能、モンテカルロ解析のための柔軟なモデリング環境 |
| foreach | R のための Foreach ループの構築 |
| forecast | 時系列と線形モデルのための機能を予測 |
| foreign | Minitab、S、SAS、SPSS、Stata、Systat、Weka、dBase などで保存されたデータの読み取り |
| formatR | 自動的な R コードの書式設定 |
| Formula | 拡張モデル式 |
| fracdiff | 部分的な ARIMA 別名 ARFIMA (p,d,q) モデルの差分 |
| gam | 一般加法モデル |
| gamlr | ガンマ ラッソ回帰 |
| gbm | 一般化ブースト回帰モデル |
| gclus | クラスタリング グラフィック |
| gdata | データ操作のためのさまざまな R プログラミング ツール |
| gee | 一般推定方程式ソルバ |
| genetics | 集団遺伝学 |
| geoR | 地球統計データの解析 |
| geoRglm | geoRglm - 一般化線形空間モデルのパッケージ |
| geosphere | 球面三角法 |
| ggmcmc | ベイズ推論からマルコフ連鎖モンテカルロ シミュレーションを分析するためのグラフィカル ツール |
| ggplot2 | グラフィックスの文法の実装 |
| glmmBUGS | WinBUGS、BRugs、OpenBUGS で稼働する一般化線形混合モデルと空間モデル |
| glmnet | ロッソと弾ネット正則一般化線形モデル |
| gmodels | モデル フィッティングのためのさまざまな R プログラミング ツール |
| gmp | 複数の倍精度演算 |
| gnm | 一般化非線形モデル |
| googlePublicData | Google のパブリック データ エクスプローラー DSPL メタデータ ファイルをビルドする R ライブラリ |
| googleVis | R と Google チャート間のインターフェイス |
| GPArotation | GPA ファクター ローテーション |
| gplots | データのプロットのためのさまざまな R プログラミングツール |
| graphics | R グラフィックス パッケージ |
| grDevices | R グラフィックス デバイスと色とフォントのサポート |
| gregmisc | Greg の他の関数 |
| grid | グリッド グラフィックス パッケージ |
| gridExtra | グリッド グラフィックスの関数 |
| growcurves | 複数のメンバーシップのランダム効果を含むベイジアン セミ モデルとノンパラメトリック成長曲線モデル |
| grpreg | グループ化された共変量と回帰モデルのための正則化パス |
| gsubfn | 文字列と関数の引数のためのユーティリティ |
| gtable | 表に grobs 配置する |
| gtools | さまざまな R プログラミング ツール |
| gWidgets | ツールキットに依存しない、対話型のGUI を構築するための gWidgets の API |
| gWidgetsRGtk2 | RGtk2 のための gWidgets のツールキット実装 |
| haplo.stats | リンケージ フェーズがあいまいな場合の形質と共変量によるハプロタイプの統計分析 |
| hbsae | 階層ベイジアン小領域推定 |
| hdrcde | 最高濃度領域との条件付き密度推定 |
| heavy | ヘビーテイル分布を使用した外れ値調和のためのパッケージ |
| hflights | 2011 年のヒューストンのの出発便 |
| HH | 統計分析とデータ表示: Heiberger とオランダ |
| HI | 入れ子になった超平面によってサポートされている分布からのシミュレーション |
| highr | R の構文強調表示 |
| Hmisc | Harrell その他 |
| htmltools | HTML 用のツール |
| httpuv | HTTP や WebSocket のサーバー ライブラリ |
| httr | URL と HTTP を操作するためのツール |
| IBrokers | 対話型のブローカー トレーダー ワークステーションへの R API |
| igraph | ネットワーク分析と可視化 |
| intervals | ポイントとの間隔を操作するためのツール |
| iplots | iPlots - R の対話型のグラフィック |
| ipred | 改善された予測因子 |
| irr | 評定者間信頼性と契約のさまざまな係数 |
| iterators | R の反復子コンストラクト |
| JavaGD | Java のグラフィック デバイス |
| JGR | JGR - R 用の Java GUI |
| kernlab | カーネルベースの機械学習ラボ |
| KernSmooth | Wand と Jones のカーネル平滑化のための関数 (1995) |
| KFKSDS | カルマン フィルター、スムーサー、外乱スムーサー |
| kinship2 | 血統関数 |
| kknn | 加重 K 最近傍 |
| klaR | 分類とビジュアル化 |
| knitr | R での動的レポート生成のための汎用パッケージ |
| ks | カーネル平滑化 |
| labeling | 軸のラベル付け |
| Lahman | Sean Lahman の野球データベース |
| lars | 最低限の角度回帰、ロッソ、前進ステップワイズ |
| lattice | ラティス グラフィックス |
| latticeExtra | ラティスに基づく余分なグラフィカル ユーティリティ |
| lava | 線形潜在変数モデル |
| lavaan | 潜在変数解析 |
| leaps | 回帰サブセット選択 |
| LearnBayes | 学習ベイズ推定のための関数 |
| limSolve | 線形逆モデルを解決する |
| lme4 | 固有と S4 を用いた線形混合効果モデル |
| lmm | 線形混合モデル |
| lmPerm | 線形モデルの順列テスト |
| lmtest | 線形回帰モデルのテスト |
| locfit | 局所回帰、可能性と密度推定 |
| lpSolve | 線形 / 整数プログラムを解決するための Lp\_solve v. 5.5 のインターフェイス |


###M ～ R で始まる R モジュール

| パッケージ名 | パッケージの説明 |
| ------------ | ------------------- |
| magic | 魔方陣の作成と調査 |
| magrittr | magrittr - R のためのフォワード パイプ演算子 |
| mapdata | 追加地図データベース |
| mapproj | 地図投影 |
| maps | 地理的な地図を描画する |
| maptools | 空間オブジェクトの読み取りと処理のためのツール |
| maptree | マッピング、剪定、グラフ·ツリー·モデル |
| markdown | R のためのマークダウンのレンダリング |
| MASS | Venables と Ripley のMASS ための関数とデータセットをサポートする |
| MasterBayes | 血統復興と分析のための ML と MCMC メソッド |
| Matrix | スパースと密行列のクラスとメソッド |
| matrixcalc | マトリックスの計算のための関数のコレクション |
| MatrixModels | 粗密行列とモデリング |
| maxent | テキスト分類をサポートする、ロー メモリ多項ロジスティック回帰 |
| maxLik | 最尤推定 |
| mcmc | マルコフ連鎖モンテカルロ |
| MCMCglmm | MCMC 一般化線型混合モデル |
| MCMCpack | マルコフ連鎖モンテカルロ (MCMC) パッケージ |
| memoise | Memoise 関数 |
| methods | 正式なメソッドとクラス |
| mgcv | GCV / AIC / REML 平滑推定と混合 GAM 計算車両 |
| mice | 連鎖式による多変量代入 |
| microbenchmark | サブ マイクロ秒の正確なタイミング関数 |
| mime | MIME の種類にファイル名をマップする |
| minpack.lm | MINPACK で見つかったレーベンバーグ·マルカート非線形最小二乗アルゴリズムへの R インターフェイス、境界のサポート |
| minqa | 2 次近似によるデリバティブのない最適化アルゴリズム |
| misc3d | その他の 3D プロット |
| miscF | その他の関数 |
| miscTools | その他のツールとユーティリティ |
| mixtools | 有限混合モデルを解析するためのツール |
| mlbench | 機械学習のベンチマーク問題 |
| mlogitBMA | 多項ロジット モデルのベイズ モデルの平均化 |
| mnormt | 多変量正規と t 分布 |
| MNP | 多項プロビット モデルをフィッティングするための R パッケージ |
| modeltools | 統計モデルのためのツールとクラス |
| mombf | モーメントと逆モーメントのベイズ因子 |
| monomvn | モノトーン missingness による多変量正規とスチューデント t データの推定 |
| moneg | ノンパラメトリック回帰モノトーン |
| mosaic | ユーティリティを教えるプロジェクト MOSAIC (mosaic-web.org) 統計と数学 |
| MSBVAR | マルコフ·スイッチング、ベイジアン、ベクトル自己回帰モデル |
| msm | 連続時間の多状態マルコフ モデルと隠れマルコフ モデル |
| multcomp | 一般的なパラメトリック モデルにおける同時推論 |
| multicool | cool-lex 順の多重集合の順列 |
| munsell | マンセル表色系 |
| mvoutlier | 堅牢な方法に基づく多変量外れ値検出 |
| mvtnorm | 多変量正規と t 分布 |
| ncvreg | SCAD と MCP ペナルティの回帰モデルの正則化パス |
| nlme | 線形と非線形混合効果モデル |
| NLP | 自然言語処理インフラストラクチャ |
| nnet | フィードフォワード ニューラル ネットワークと多項対数線形モデル |
| numbers | 数論的関数 |
| numDeriv | 正確な数値微分 |
| openNLP | Apache OpenNLP ツール インターフェイス |
| openNLPdata | Apache OpenNLP Jar と基本英語の言語モデル |
| OutlierDC | 打ち切りデータのための分位回帰を用いた外れ値検出 |
| OutlierDM | 複製されたハイスループット·データの外れ値検出 |
| outliers | 外れ値のテスト |
| pacbpred | スパース加法モデルにおける PAC ベイズ推定と予測 |
| parallel | R での並列処理のサポート |
| partitions | 整数の加法パーティション |
| party | 再帰 Partytioning 研究チーム |
| PAWL | PAWL アルゴリズムの実装 |
| pbivnorm | ベクトル化変量正規 CDF |
| pcaPP | 射影追跡によるロバスト PCA |
| permute | データの制限された順列を生成するための関数 |
| pls | 部分最小二乗法と主成分回帰 |
| plyr | データの分割、適用、組み合わせのためのツール |
| png | PNG 画像の読み取りと書き込み |
| polynom | 一変多項式の操作のためのクラスを実装する関数のコレクション |
| PottsUtils | Potts モデルのユーティリティ関数 |
| predmixcor | 補正された特徴選択バイアスとベイズ混合モデルに基づく分類ルール |
| PresenceAbsence | プレゼンス不在のモデル評価 |
| prodlim | 製品制限推定。打ち切りイベント履歴 (生存率) 分析のための Kaplan-Meier と Aalen-Johansson メソッド |
| profdpm | ディリクレ過程混合プロフィール |
| profileModel | さまざまなモデル クラスの推論機能をプロファイリングするためのツール |
| proto | プロトタイプ オブジェクト ベースのプログラミング |
| pscl | 政治学計算研究所、スタンフォード大学 |
| psych | 心理的、心理、パーソナリティ研究のための手順 |
| quadprog | 2 次計画問題を解決するための関数 |
| quantreg | 分位回帰 |
| qvcalc | 統計モデルの要因の影響のための準差異 |
| R.matlab | MAT ファイルの読み取り / 書き込みと R-to-MATLAB 接続 |
| R.methodsS3 | S3 メソッドを定義するためのユーティリティ関数 |
| R.oo | 参照の有無に依存しない R のオブジェクト指向プログラミング |
| R.utils | さまざまなプログラミング ユーティリティ |
| R2HTML | R オブジェクトの HTML のエクスポート |
| R2jags | R から jags を実行するためのパッケージ |
| R2OpenBUGS | R からの OpenBUGS の実行 |
| R2WinBUGS | R / S-PLUS からの WinBUGS と OpenBUGS の実行 |
| ramps | RAMPS とベイズ地球統計モデル |
| RandomFields | 確率場のシミュレーションと解析 |
| randomForest | Breiman と Cutler の分類と回帰のためのランダム フォレスト |
| RArcInfo | Arc/Info V7.x バイナリ カバレッジからデータをインポートする関数 |
| raster | raster: 地理データ分析とモデリング |
| rbugs | R、OpenBugs、Beyond の融合 |
| RColorBrewer | ColorBrewer パレット |
| Rcpp | R と C++ のシームレスな統合 |
| RcppArmadillo | Armadillo テンプレート線形代数ライブラリのための Rcpp 統合 |
| rcppbugs | Cppbugs のための R のバインディング |
| RcppEigen | Eigen テンプレート線形代数ライブラリのための Rcpp 統合 |
| RcppExamples | R と C++ のインターフェイスへの Rcpp の使用例 |
| RCurl | R の一般的なネットワーク (HTTP/FTP/...) のクライアント インターフェイス |
| relimp | 回帰モデルの効果の相対的寄与 |
| reshape | データの柔軟な変形 |
| reshape2 | データの柔軟な変形: 変形パッケージの再起動 |
| rgdal | 地理空間データ抽象ライブラリ用のバインディング |
| rgeos | ジオメト リエンジンへのインタフェイス – オープンソース (GEOS) |
| rgl | 3 次元可視化デバイス システム (OpenGL) |
| RGraphics | 書籍 R グラフィック第 2 版からのデータと関数 |
| RGtk2 | Gtk 2.8.0 以降のための R のバインディング |
| RJaCGH | CGH 配列の分析のためのリバーシブル ジャンプ MCMC |
| rjags | MCMC を用いたベイジアン グラフィカル モデル |
| rJava | Java インターフェイスへの低レベル R |
| RJSONIO | JSON、JavaScript オブジェクト表記に R オブジェクトをシリアル化する |
| robCompositions | 組成データのロバスト推定 |
| robustbase | 基本的なロバスト統計 |
| RODBC | ODBC データベース アクセス |
| rootSolve | 非線形ルートの検出、常微分方程式の平衡と定常状態解析 |
| roxygen | R の文芸的プログラミング |
| roxygen2 | R のインソース ドキュメント |
| rpart | 再帰分割と回帰ツリー |
| rrcov | 高降伏点でスケーラブルなロバスト推定量 |
| rscproxy | statconn: 移植可能な C 形式のインターフェイスを R (StatConnector) に提供する |
| RSGHB | 階層ベイズ推定のための関数: 柔軟なアプローチ |
| RSNNS | Stuttgart Neural Network Simulator (SNNS) を使用した R のニューラル ネットワーク |
| RTextTools | 教師あり学習による自動テキスト分類 |
| RUnit | R ユニット テスト フレームワーク |
| runjags | JAGS の MCMC モデルのためのインターフェイス ユーティリティ、並列コンピューティング メソッド、追加ディストリビューション |
| Runuran | UNU.RAN ランダム変量発生器への R インターフェイス |
| rworldmap | グローバル データ、ベクター、ラスターのマッピング |
| rworldxtra | 高解像度の国境 |


###S ～ Z で始まる R モジュール

| パッケージ名 | パッケージの説明 |
| ------------ | ------------------- |
| SampleSizeMeans | 通常の方法のサンプル サイズの計算 |
| SampleSizeProportions | 2 つの二項比率の違いを推定する際にサンプル サイズ要件を計算する |
| sandwich | ロバスト共分散行列推定量 |
| sbgcop | セミパラメトリック ベイズ ガウス·コピュラの推定と補完 |
| scales | グラフィックスのスケール関数 |
| scapeMCMC | MCMC 診断プロット |
| scatterplot3d | 3D 散布図 |
| sciplot | 要因分析のための科学的なグラフ関数 |
| segmented | ブレークポイント / チェンジポイント推定と回帰モデルでのセグメント化されたリレーションシップ |
| sem | 構造方程式モデル |
| seriation | 系列下のインフラストラクチャ |
| setRNG | (通常) 乱数ジェネレーターとシードを設定する |
| sgeostat | S + での地球統計モデリングのためのオブジェクト指向フレームワーク |
| shapefiles | ESRI シェープファイルの読み取りと書き込み |
| shiny | R のための Web アプリケーション フレームワーク |
| SimpleTable | 未測定交絡の存在下での 2 x 2 と 2 x 2 x K テーブルの因果効果のためのベイズ推論と感度解析 |
| slam | スパース軽量配列と行列 |
| smoothSurv | 平滑化誤差分布と生存回帰 |
| sna | ソーシャル ネットワーク分析のためのツール |
| snow | ワークステーションの簡易ネットワーク |
| SnowballC | C libstemmer UTF-8 ライブラリに基づく Snowball ステマー |
| snowFT | ワークステーションのフォールト トレラント簡易ネットワーク |
| sp | 空間データ用のクラスとメソッド |
| spacetime | 時空間データ用のクラスとメソッド |
| SparseM | スパース線形代数 |
| spatial | クリギングとポイント パターン解析のための関数 |
| spBayes | 単変量と多変量の時空間モデル |
| spdep | 空間依存性: 重み付けスキーム、統計、モデル |
| spikeslab | スパイクとスラブ回帰を用いた予測と変数選択 |
| splancs | 空間と時空ポイント パターン分析 |
| splines | 回帰スプライン関数とクラス |
| spTimer | R を用いた時空間ベイズ モデリング |
| stats | R Stats パッケージ |
| stats4 | S4 クラスを用いた統計関数 |
| stochvol | 確率的ボラティリティ (SV) モデルの効率的なベイズ推定 |
| stringr | 文字列を操作し易くする |
| strucchange | テスト、監視、データ構造の変更 |
| stsm | 構造的時系列モデル |
| stsm.class | 構造時系列モデルのためのクラスとメソッド |
| SuppDists | 補足分布 |
| survival | 生存分析 |
| svmpath | svmpath: SVM パス アルゴリズム |
| tau | テキスト分析ユーティリティ |
| tcltk | Tcl/Tk インターフェイス |
| tcltk2 | Tcl/Tk 追加機能 |
| TeachingDemos | 教育と学習のためのデモンストレーション |
| tensorA | 名前付きのインデックスを使用した高度なテンソル演算 |
| testthat | Testthat コード。テストを楽しくするツール |
| textcat | N グラム ベースのテキスト分類 |
| textir | テキスト分析のための逆回帰 |
| tfplot | タイム フレーム ユーザー ユーティリティ |
| tframe | カーネルのコーディング時間枠 |
| tgp | ベイズ ツリー ガウス過程モデル |
| TH.data | TH のデータ アーカイブ |
| timeDate | Rmetrics - 時系列とカレンダー オブジェクト |
| tm | テキスト マイニング パッケージ |
| ツール | パッケージ開発用ツール |
| translations | R 翻訳パッケージ |
| tree | 分類と回帰ツリー |
| tseries | 時系列解析と計算ファイナンス |
| tsfa | 時系列因子分析 |
| tsoutliers | 時系列での外れ値の自動検出 |
| TSP | 巡回セールスマン問題 (TSP) |
| UsingR | テキスト「入門統計学での R の使用」のためのデータ セット |
| utils | R ユーティリティ パッケージ |
| varSelectIP | 客観的なベイズ モデル選択 |
| vcd | カテゴリカル データの視覚化 |
| vegan | コミュニティ エコロジー パッケージ |
| VGAM | ベクター一般化線形モデルと加法モデル |
| VIF | VIF 回帰: 大規模データの高速回帰アルゴリズム |
| whisker | R 用 {{mustache}}、logicless テンプレート |
| wordcloud | Word クラウド |
| XLConnect | R 用 Excel のコネクタ |
| XML | R と S-Plus 内の XML を解析し、生成するためのツール |
| xtable | LaTeX または HTML にテーブルをエクスポートする |
| xts | 拡張可能な時系列 |
| yaml | R と YAML 間のデータ変換メソッド |
| zic | ゼロ膨張のカウント モデルのベイズ推論 |
| zipfR | 単語頻度分布のための統計モデル |
| zoo | 規則 / 不規則時系列のための S3 インフラストラクチャ (Z の順序観察) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0330_2016------>