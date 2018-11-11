---
title: Azure Machine Learning CLI 拡張機能について
description: Azure Machine Learning 用の機械学習 CLI 拡張機能について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: 45ed1867d6d151250340bb21450b4b0d9b00e993
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243149"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Azure Machine Learning CLI とは

Azure Machine Learning コマンド ライン インターフェイス (CLI) の拡張機能は、Azure Machine Learning サービスを使用するデータ サイエンティストおよび開発者向けです。 短時間で次のような機械学習ワークフローを自動化して、運用環境に導入できます。
+ 実験を実行して機械学習モデルを作成します

+ 顧客が使用できるように機械学習モデルを登録します

+ 機械学習モデルのライフサイクルをパッケージ化、デプロイ、追跡します

この機械学習 CLI は [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) の拡張機能であり、Azure Machine Learning サービス用の Python ベースの <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> を基にして開発されました。

> [!NOTE]
> この CLI は現在は早期プレビュー版であり、今後更新されます。

## <a name="installing-and-uninstalling"></a>インストールとアンインストール

プレビュー PyPi インデックスから次のコマンドを使用して CLI をインストールできます。
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.68-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

次のコマンドを使用して、CLI を削除できます。
```AzureCLI
az extension remove -n azure-cli-ml
```

上記の **remove** と **add** の手順を使用して、CLI を更新できます。

## <a name="using-the-cli-vs-the-sdk"></a>CLI の使用と SDK の使用の比較
CLI は、開発運用担当者による自動化に、または継続的インテグレーションとデリバリー パイプラインの一部としての自動化に適しています。 頻度が低く高度にパラメーター化されたタスクの処理に最適化されています。 

たとえば、次のようになります。
- コンピューティングのプロビジョニング
- パラメーター化された実験の送信
- モデルの登録、イメージの作成
- サービスのデプロイ

データ サイエンティストは Azure ML SDK を使用することをお勧めします。

## <a name="common-machine-learning-cli-commands"></a>機械学習 CLI の一般的なコマンド
> [!NOTE]
> 以下のコマンドを問題なく実行するために使用できるサンプル ファイルについては、[こちら](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)をご覧ください。

Azure portal クラウド シェルなどの任意のコマンド ライン環境でサービスを操作するには、充実した `az ml` コマンドのセットを使用します。

一般的なコマンドの例を次に示します。

### <a name="workspace-creation--compute-setup"></a>ワークスペースの作成とコンピューティングのセットアップ

+ 機械学習の最上位レベルのリソースである Azure Machine Learning サービス ワークスペースを作成します。
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ このワークスペースを既定で使用するように CLI を設定します。
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ DSVM (データ サイエンス VM) を作成します。 分散トレーニング用の BatchAI クラスターまたはデプロイ用の AKS クラスターを作成することもできます。
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>実験の送信
+ 実験を送信するにはプロジェクト (実行構成) にアタッチします。 これは、実験の実行の追跡に使用されます。
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ 選択したコンピューティング ターゲット上の Azure Machine Learning サービスに対して実験を送信します。 この例では、ローカル コンピューティング環境に対して実行します。 Conda 環境ファイルが Python 依存関係をキャプチャすることを確認します。

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ 送信された実験の一覧を表示します。
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>モデルの登録、イメージの作成デプロイ

+ Azure Machine Learning にモデルを登録します。
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 機械学習モデルと依存関係を格納するイメージを作成します。 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ ACI と AKS を含むターゲットにパッケージ化されたモデルをデプロイします。
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>全コマンドの一覧
CLI 拡張機能 (およびそのサポートされているパラメーター) のコマンドの完全な一覧を表示するには、```az ml COMMANDNAME -h``` を実行します。 
