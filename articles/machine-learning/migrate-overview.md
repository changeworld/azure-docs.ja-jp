---
title: 'ML スタジオ (クラシック): Azure Machine Learning に移行する'
description: 最新のデータ サイエンス プラットフォームのために、スタジオ (クラシック) から Azure Machine Learning に移行します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 10/21/2021
ms.openlocfilehash: 384b1b11e272cab8386a5a4f3344c36e99959254
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561936"
---
# <a name="migrate-to-azure-machine-learning"></a>Azure Machine Learning への移行 

> [!IMPORTANT]
> Machine Learning Studio (クラシック) のサポートは、2024 年 8 月 31 日に終了します。 その日までに、[Azure Machine Learning](./overview-what-is-azure-machine-learning.md) に切り替えすることをお勧めします。
>
> 2021 年 12 月 1 日以降、新しい Machine Learning Studio (クラシック) リソースは作成できません。 2024 年 8 月 31 日まで、既存の Machine Learning Studio (クラシック) リソースを引き続き使用できます。  
>
> ML Studio (クラシック) のドキュメントは廃止予定であり、今後更新されない可能性があります。

Studio (クラシック) から Azure Machine Learning に移行する方法について学習します。 Azure Machine Learning によって、ノーコードとコードファーストのアプローチを組み合わせた最新のデータ サイエンス プラットフォームが提供されます。

これは、基本的な「リフト アンド シフト」移行のガイドです。 既存の機械学習ワークフローを最適化する場合、または機械学習プラットフォームを最新化する場合の、デジタル調査ツール、ワークシート、計画テンプレートなどの追加リソースについては、[Azure Machine Learning 導入フレームワーク](https://aka.ms/mlstudio-classic-migration-repo)を参照してください。

![Azure ML 導入フレームワーク](./media/migrate-overview/aml-adoption-framework.png)

## <a name="recommended-approach"></a>推奨される方法

Azure Machine Learning に移行するには、次の方法をお勧めします。

> [!div class="checklist"]
> * 手順 1: Azure Machine Learning を評価する
> * 手順 2: 戦略と計画を定義する
> * 手順 3: 実験と Web サービスを再構築する
> * 手順 4: クライアント アプリを統合する
> * 手順 5: スタジオ (クラシック) アセットをクリーンアップする
> * 手順 6: シナリオを確認して展開する


## <a name="step-1-assess-azure-machine-learning"></a>手順 1: Azure Machine Learning を評価する
1. [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) の特長、コスト、アーキテクチャについて学習します。

1. Azure Machine Learning とスタジオ (クラシック) の機能を比較します。

    >[!NOTE]
    > Azure Machine Learning の **デザイナー** 機能では、スタジオ (クラシック) と同様のドラッグ アンド ドロップ操作を利用できます。 しかしながら、Azure Machine Learning には、堅牢な[コードファースト ワークフロー](concept-model-management-and-deployment.md)も代替手段として提供されています。 スタジオ (クラシック) エクスペリエンスに最もよく似ているため、この移行シリーズではデザイナーに焦点を当てています。

    [!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

3. Azure Machine Learning デザイナーで、ご自分の重要なスタジオ (クラシック) モジュールがサポートされていることを確認します。 詳細については、後述の「[スタジオ (クラシック) とデザイナーのコンポーネントマッピング](#studio-classic-and-designer-component-mapping)」の表を参照してください。

4. [Azure Machine Learning ワークスペースを作成](how-to-manage-workspace.md?tabs=azure-portal)します。

## <a name="step-2-define-a-strategy-and-plan"></a>手順 2: 戦略と計画を定義する

1. 業務上の正当な理由と期待される成果を定義します。
1. 実施可能な Azure Machine Learning の導入計画をビジネスの成果に合わせて調整します。
1. 変更のための人材、プロセス、環境を用意します。

計画ドキュメント テンプレートを含むリソースの計画については、[Azure Machine Learning 導入フレームワーク](https://aka.ms/mlstudio-classic-migration-repo)を参照してください。 

## <a name="step-3-rebuild-your-first-model"></a>手順 3: 最初のモデルをリビルドする

戦略を定義した後、最初のモデルを移行します。

1. [データセットを Azure Machine Learning に移行します](migrate-register-dataset.md)。
1. デザイナーを使用して、[実験を再構築します](migrate-rebuild-experiment.md)。
1. デザイナーを使用して、[Web サービスを再デプロイします](migrate-rebuild-web-service.md)。

    >[!NOTE]
    > Azure Machine Learning では、[データセット](how-to-create-register-datasets.md)の移行、[トレーニング](how-to-set-up-training-targets.md)、[デプロイ](how-to-deploy-and-where.md)のためのコードファースト ワークフローもサポートされています。

## <a name="step-4-integrate-client-apps"></a>手順 4: クライアント アプリを統合する

1. スタジオ (クラシック) の Web サービスを呼び出すクライアント アプリケーションを、新しい [Azure Machine Learning エンドポイント](migrate-rebuild-integrate-with-client-app.md)を使用するように変更します。

## <a name="step-5-cleanup-studio-classic-assets"></a>手順 5: スタジオ (クラシック) アセットをクリーンアップする

1. 追加料金が発生しないように、[スタジオ (クラシック) アセットをクリーンアップします](./classic/export-delete-personal-data-dsr.md)。 Azure Machine Learning ワークロードの検証が終了するまで、フォールバックできるようにアセットを保持することをお勧めします。

## <a name="step-6-review-and-expand-scenarios"></a>手順 6: シナリオを確認して展開する

1. ベスト プラクティスのモデル移行を確認し、ワークロードを検証します。
1. シナリオを展開し、追加のワークロードを Azure Machine Learning に移行します。


## <a name="studio-classic-and-designer-component-mapping"></a>スタジオ (クラシック) とデザイナーのコンポーネントマッピング

スタジオ (クラシック) の実験をデザイナーで再構築するときに使用するモジュールを確認するには、次の表を参照してください。


> [!IMPORTANT]
> デザイナーのモジュール実装には、スタジオ (クラシック) のように C# パッケージではなく、オープンソースの Python パッケージが使われています。 この違いにより、デザイナー コンポーネントの出力は、対応するスタジオ (クラシック) とは若干異なる場合があります。


|カテゴリ|スタジオ (クラシック) モジュール|後継のデザイナー コンポーネント|
|--------------|----------------|--------------------------------------|
|データの入力と出力|- データの手動入力 </br> - データのエクスポート </br> - データのインポート </br> - トレーニング済みのモデルの読み込み </br> - zip 形式のデータセットのアンパック|- データの手動入力 </br> - データのエクスポート </br> - データのインポート|
|データ形式の変換|- CSV への変換 </br> - データセットへの変換 </br> - ARFF への変換 </br> - SVMLight への変換 </br> - TSV への変換|- CSV への変換 </br> - データセットへの変換|
|データ変換 - 操作|- 列の追加</br> - 行の追加 </br> - SQL 変換の適用 </br> - 見つからないデータのクリーンアップ </br> - インジケーター値への変換 </br> - メタデータの編集 </br> - データの結合 </br> - 重複する行の削除 </br> - データセット内の列の選択 </br> - 列変換の選択 </br> - SMOTE </br> - グループ カテゴリ値|- 列の追加</br> - 行の追加 </br> - SQL 変換の適用 </br> - 見つからないデータのクリーンアップ </br> - インジケーター値への変換 </br> - メタデータの編集 </br> - データの結合 </br> - 重複する行の削除 </br> - データセット内の列の選択 </br> - 列変換の選択 </br> - SMOTE|
|データ変換 - 拡大と縮小 |- クリップ値 </br> - データのビンへのグループ化 </br> - データの正規化 </br>- 主成分分析 |- クリップ値 </br> - データのビンへのグループ化 </br> - データの正規化|
|データ変換 - サンプルおよび分割|- パーティションとサンプル </br> - データの分割|- パーティションとサンプル </br> - データの分割|
|データ変換 - フィルター |- フィルターの適用 </br> - FIR フィルター </br> - IIR フィルター </br> - 中央値フィルター </br> - 移動平均フィルター </br> - しきい値フィルター </br> - ユーザー定義フィルター||
|データ変換 - カウントを使用した学習 |- カウント変換の構築 </br> - カウント テーブルのエクスポート </br> - カウント テーブルのインポート </br> - カウント変換のマージ</br>  - カウント テーブル パラメーターの変更||
|特徴選択 |- フィルターに基づく特徴選択 </br> - Fisher 線形判別分析  </br> - 順列の特徴量の重要度 |- フィルターに基づく特徴選択 </br>  - 順列の特徴量の重要度|
| モデル - 分類| - 多クラス デシジョン フォレスト </br> - 多クラス デシジョン ジャングル  </br> - 多クラスのロジスティック回帰  </br>- 多クラス ニューラル ネットワーク  </br>- One-vs-All Multiclass </br>- 2 クラス平均化パーセプトロン </br>- 2 クラスのベイズ ポイント マシン </br>- 2 クラスの増幅デシジョン ツリー  </br> - 2 クラス デシジョン フォレスト  </br> - 2 クラス デシジョン ジャングル  </br> - 2 クラス ローカル詳細 SVM </br> - 2 クラス ロジスティック回帰  </br> - 2 クラス ニューラル ネットワーク </br> - 2 クラス サポート ベクター マシン  | - 多クラス デシジョン フォレスト </br>  - 多クラスの増幅デシジョン ツリー  </br> - 多クラスのロジスティック回帰 </br> - 多クラス ニューラル ネットワーク </br> - One-vs-All Multiclass  </br> - 2 クラス平均化パーセプトロン  </br> - 2 クラスの増幅デシジョン ツリー  </br> - 2 クラス デシジョン フォレスト </br>- 2 クラス ロジスティック回帰 </br> - 2 クラス ニューラル ネットワーク </br>- 2 クラス サポート ベクター マシン  |
| モデル - クラスタリング| - K-Means クラスタリング| - K-Means クラスタリング|
| モデル - 回帰| - ベイジアン線形回帰  </br> - 増幅デシジョン ツリーの回帰  </br>- デシジョン フォレスト回帰  </br> - 高速フォレスト分位点回帰  </br> - 線形回帰 </br> - ニューラル ネットワーク回帰 </br> - 序数回帰  ポワソン回帰| - 増幅デシジョン ツリーの回帰  </br>- デシジョン フォレスト回帰  </br> - 高速フォレスト分位点回帰 </br> - 線形回帰  </br> - ニューラル ネットワーク回帰 </br> - ポワソン回帰|
| モデル - 異常検出| - 1 クラス SVM  </br> - PCA ベースの異常検出 | - PCA ベースの異常検出|
| 機械学習 - 評価  | - モデルのクロス検証  </br>- モデルの評価  </br>- レコメンダーの評価 | - モデルのクロス検証  </br>- モデルの評価 </br> - レコメンダーの評価|
| 機械学習 - トレーニング| - スイープ クラスタリング  </br> - 異常検出モデルのトレーニング </br>- クラスタリング モデルのトレーニング  </br> - マッチボックス レコメンダーのトレーニング -</br> モデルのトレーニング  </br>- モデルのハイパーパラメーターの調整| - 異常検出モデルのトレーニング  </br> - クラスタリング モデルのトレーニング </br> - モデルのトレーニング -</br> - PyTorch モデルのトレーニング  </br>- SVD レコメンダーのトレーニング  </br>- ワイドかつディープなレコメンダーのトレーニング </br>- モデルのハイパーパラメーターの調整|
| 機械学習 - スコア| - 変換の適用  </br>- クラスターへのデータの割り当て  </br>- マッチボックス レコメンダーのスコアリング </br> - モデルのスコア付け|- 変換の適用  </br> - クラスターへのデータの割り当て </br> - 画像モデルのスコア付け  </br> - モデルのスコア付け </br>- SVD レコメンダーのスコア付け </br> \- ワイドかつディープなレコメンダーのスコア付け|
| OpenCV ライブラリ モジュール| - イメージのインポート </br>- 事前トレーニング済みカスケード イメージ分類 | |
| Python 言語モジュール| - Python スクリプトの実行| - Python スクリプトの実行  </br> - Python モデルの作成 |
| R 言語モジュール  | - R スクリプトの実行  </br> - R モデルの作成| - R スクリプトの実行|
| 統計関数 | - 算術演算の適用 </br>- 基本統計のコンピューティング  </br>- 線形相関関係のコンピューティング  </br>- 確率関数の評価  </br>- 離散値の置換  </br>- データの集計  </br>- T 検定を使用した仮説のテスト| - 算術演算の適用  </br>- データの集計|
| Text Analytics| - 言語の検出  </br>- テキストからのキー フレーズ抽出  </br>- テキストからの N Gram 特徴抽出  </br>- 特徴ハッシュ </br>- Latent Dirichlet Allocation  </br>- 固有表現認識 </br>- テキストの前処理  </br>- Vowpal Wabbit バージョン 7-10 モデルのスコアリング  </br>- Vowpal Wabbit バージョン 8 モデルのスコアリング </br>- Vowpal Wabbit バージョン 7-10 モデルのトレーニング  </br>- Vowpal Wabbit バージョン 8 モデルのトレーニング |- 単語からベクトルへの変換 </br> - テキストからの N Gram 特徴抽出 </br>- 特徴ハッシュ  </br>- Latent Dirichlet Allocation </br>- テキストの前処理  </br>- Vowpal Wabbit モデルのスコアリング </br> - Vowpal Wabbit モデルのトレーニング|
| タイム シリーズ| - 時系列の異常検出 | |
| Web サービス | - 入力 </br> - 出力 | - 入力 </br>  - 出力|
| Computer Vision| | - イメージ変換の適用 </br> - イメージ ディレクトリへの変換 </br> - イメージ変換の初期化 </br> - イメージ ディレクトリの分割  </br> - DenseNet 画像分類   </br>- ResNet 画像分類 |

個々のデザイナー コンポーネントを使用する方法の詳細については、[デザイナー コンポーネントのリファレンス](./component-reference/component-reference.md)に関するページを参照してください。

### <a name="what-if-a-designer-component-is-missing"></a>デザイナー コンポーネントが見つからない場合はどうすればよいですか。

Azure Machine Learning デザイナーには、スタジオ (クラシック) の最も人気のあるモジュールが含まれています。 また、最新の機械学習手法を利用する新しいモジュールも含まれています。 

デザイナーにモジュールがないために移行が妨げられている場合は、[サポート チケットを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してお問い合わせください。

## <a name="example-migration"></a>移行の例

次の実験移行では、スタジオ (クラシック) と Azure Machine Learning の違いについていくつか取り上げます。

### <a name="datasets"></a>データセット

スタジオ (クラシック) では、**データセット** はワークスペースに保存され、スタジオ (クラシック) でのみ使用できました。

![automobile-price-classic-dataset](./media/migrate-overview/studio-classic-dataset.png)

Azure Machine Learning では、**データセット** はワークスペースに登録され、すべての Azure Machine Learning で使用できます。 Azure Machine Learning データセットの利点の詳細については、[データ アクセスのセキュリティ保護](concept-data.md#reference-data-in-storage-with-datasets)に関するページを参照してください。

![automobile-price-aml-dataset](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>パイプライン

スタジオ (クラシック) では、作業の処理ロジックを **実験** に含めました。 ドラッグ アンド ドロップ モジュールを使用して実験を作成しました。

![automobile-price-classic-experiment](./media/migrate-overview/studio-classic-experiment.png)

Azure Machine Learning では、作業の処理ロジックを **パイプライン** に含めます。 パイプラインは、ドラッグ アンド ドロップ モジュールを使用するか、コードを記述すして作成できます。

![automobile-price-aml-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Web サービス エンドポイント

スタジオ (クラシック) では、**リアルタイム予測に REQUEST/RESPOND API** を使用し、バッチ予測や再トレーニングには **BATCH EXECUTION API** が使用されました。

![automobile-price-classic-webservice](./media/migrate-overview/studio-classic-web-service.png)

Azure Machine Learning では、リアルタイムの予測に **リアルタイム エンドポイント** を使用し、バッチ予測や再トレーニングには **パイプライン エンドポイント** を使用します。

![automobile-price-aml-endpoint](./media/migrate-overview/aml-endpoint.png)

## <a name="next-steps"></a>次のステップ

この記事では、Azure Machine Learning に移行するための高レベルの要件について説明しました。 詳細な手順については、スタジオ (クラシック) 移行シリーズの他の記事を参照してください。

1. **移行の概要** に関するドキュメントを参照してください。
1. [データセットを移行します](migrate-register-dataset.md)。
1. [スタジオ (クラシック) のトレーニング パイプラインを再構築します](migrate-rebuild-experiment.md)。
1. [スタジオ (クラシック) の Web サービスを再構築します](migrate-rebuild-web-service.md)。
1. [Azure Machine Learning の Web サービスをクライアント アプリと統合します](migrate-rebuild-integrate-with-client-app.md)。
1. [R スクリプトの実行を移行します](migrate-execute-r-script.md)。

その他の移行リソースについては、[Azure Machine Learning 導入フレームワーク](https://aka.ms/mlstudio-classic-migration-repo)を参照してください。