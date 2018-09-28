---
title: Azure Machine Learning サービスへの移行
description: 以前のバージョンから最新バージョンの Azure Machine Learning サービスにアップグレードまたは移行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 2182a39836f02596d22168722e6ece7a2872dccc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969485"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>最新バージョンの Azure Machine Learning サービスに移行する 

**ワークベンチ (プレビュー) アプリケーションがインストールされているかまたは実験とモデル管理のプレビュー アカウントがある (あるいはその両方の) 場合は、この記事を使用して、最新バージョンに移行します。**  プレビュー ワークベンチがインストールされていないか、実験とモデルのどちらの管理アカウントも持っていない場合は、何も移行する必要はありません。

## <a name="what-can-i-migrate"></a>何を移行できるか
Azure Machine Learning サービスの最初のプレビューで作成されたほとんどの成果物は、独自のローカル記憶域またはクラウド記憶域に格納されます。 これらの成果物は消失しません。 移行するには、成果物を、更新された Azure Machine Learning サービスに再登録します。 

次の表と記事では、最新バージョンの Azure Machine Learning サービスへの移行の前後に、既存のアセットとリソースでできる事柄について説明します。 以前のバージョンとアセットもしばらくの間は引き続き使用できます ([遷移サポート タイムラインを参照してください。](overview-what-happened-to-workbench.md#timeline))。

|旧バージョンのアセットまたはリソース|移行可能?|Actions|
|-----------------|:-------------:|-------------|
|Machine Learning モデル (ローカル ファイルとして)|[はい]|なし。 前と同様に動作します。|
|モデルの依存関係およびスキーマ (ローカル ファイルとして)|[はい]|なし。 前と同様に動作します。|
|プロジェクト|[はい]|[ローカル フォルダーを新しいワークスペースにアタッチします](#projects)。|
|実行履歴|いいえ |しばらくの間は[ダウンロード可能です](#history)。|
|データ準備ファイル|いいえ |新しい Azure Machine Learning Data Prep SDK または Azure Databricks を使用して、モデリングに[任意のサイズのデータ セットを準備します](#dataprep)。|
|コンピューティング ターゲット|いいえ |新しいワークスペースに登録します。|
|登録済みモデル|いいえ |新しいワークスペースにモデルを再登録します。|
|登録済みマニフェスト|いいえ |なし。 新しいワークスペースにはマニフェストが概念として存在しなくなりました。|
|登録済み|いいえ |新しいワークスペースに展開 Docker イメージを再作成します。|
|デプロイ済み Web サービス|いいえ |なし。 引き続きそのとおり機能します。 <br/>または[最新バージョンを使用して再びデプロイします](#services)。|
|実験および <br/>モデル管理アカウント|いいえ |代わりに[ワークスペース](#resources)を作成します。|
|Machine Learning CLI および SDK|いいえ |新しい作業に新しい [CLI](reference-azure-machine-learning-cli.md) と [SDK](http://aka.ms/aml-sdk) を使用します。|


詳細については、「[このリリースでの変更点](overview-what-happened-to-workbench.md)」を参照してください。

>[!Warning]
>この記事は、Azure Machine Learning Studio のユーザーを対象にはしていません。 これはワークベンチ (プレビュー) アプリケーションがインストールされているか、実験とモデルの管理プレビュー アカウントを持っている Azure Machine Learning サービスのお客様向けです。

<a name="resources"></a>

## <a name="azure-resources"></a>Azure リソース

実験アカウント、モデル管理アカウント、および Machine Learning コンピューティング環境などのリソースは、最新バージョンの Azure Machine Learning サービスに移行できません。 アセットがどのくらいの期間機能し続けるかについては、[タイムライン](overview-what-happened-to-workbench.md#timeline)を参照してください。

[Azure portal ](quickstart-get-started.md)で Azure Machine Learning ワークスペースを作成することで、最新バージョンを開始します。 ポータルのワークスペース ダッシュボードは、Edge、Chrome、および Firefox ブラウザーでのみでサポートされます。

この新しいワークスペースは最上位のサービス リソースであり、Azure Machine Learning サービスのすべての最新機能を使用することができます。 [ワークスペースとアーキテクチャの詳細についてはこちらをご覧ください](concept-azure-machine-learning-architecture.md)。

<a name="projects"></a>

## <a name="projects"></a>プロジェクト

プロジェクトをクラウドのワークスペースに配置する代わりに、最新リリースでは、プロジェクトはローカル コンピューター上のディレクトリに配置されるようになりました。 [最新アーキテクチャの図をご覧ください](concept-azure-machine-learning-architecture.md)。 

ファイルとスクリプトを含むローカル ディレクトリを使用し続けるには、ディレクトリの名前を ['experiment.submit'](http://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python コマンドで指定するか、または 'az ml project attach' CLI コマンドを使用します。

例: 
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>デプロイ済み Web サービス

Web サービスを移行するには、新しい SDK または CLI を使用して、モデルを新しいデプロイ ターゲットに再デプロイします。 元のスコアリング ファイル、モデル ファイル依存関係ファイル、環境ファイル、およびスキーマ ファイルを変更する必要はありません。 

最新バージョンでは、モデルは Web サービスとして [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) または [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS) クラスターにデプロイされます。 

詳細については、次の記事を参照してください。
+ [ACI にデプロイする](how-to-deploy-to-aci.md)
+ [AKS にデプロイする](how-to-deploy-to-aks.md)
+ [チュートリアル: Azure Machine Learning サービスを使用してモデルをデプロイする](tutorial-deploy-models-with-aml.md)

[前の CLI のサポートが終了すると](overview-what-happened-to-workbench.md#timeline)、元はモデル管理アカウントを使用してデプロイされた Web サービスは管理できなくなります。 ただし、これらの Web サービスは Azure Container Service (ACS) がサポートされている限りは引き続き機能します。

<a name="history"></a>

## <a name="run-history-records"></a>実行履歴の記録

古いワークスペースにある既存の実行履歴に追加し続けることはできませんが、前の CLI を使用して、手持ちの履歴をエクスポートすることはできます。 [前の CLI のサポートが終了すると](overview-what-happened-to-workbench.md#timeline)、これらの実行履歴をエクスポートすることはできなくなります。

モデルのトレーニングと、新しい CLI と SDK を使用した実行履歴の追跡を開始します。 方法については、「[チュートリアル: Azure Machine Learning サービスを使用したモデルのトレーニング](tutorial-train-models-with-aml.md)」から参照できます。

以前の CLI を使用して実行履歴をエクスポートするには、次のようにします。

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>データ準備ファイル
データ準備ファイルは、ワークベンチを使用しないと移植できません。 新しい Azure Machine Learning Data Prep SDK または Azure Databricks をビッグ データ セットに使用すれば、モデリング用に任意のサイズのデータ セットを引き続き準備できます。  [データ準備 SDK を取得する方法をご覧ください](how-to-data-prep.md)。 

## <a name="next-steps"></a>次の手順

ワークスペースの作成、プロジェクトの作成、スクリプトの実行、および最新のバージョンの Azure Machine Learning サービスを使用したスクリプトの実行履歴の調査を行う方法を示すクイック スタートとして、[Azure Machine Learning サービス](quickstart-get-started.md)を開始してみてください。

このワークフローをさらに詳しく体験するには、Azure Machine Learning サービスを使用してモデルをトレーニングおよびデプロイするための詳細な手順が記載されている、記述の省略がないチュートリアルに従ってください。 

> [!div class="nextstepaction"]
> [チュートリアル: モデルのトレーニングとデプロイ](tutorial-train-models-with-aml.md)
