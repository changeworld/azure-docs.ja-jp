---
title: ML モデルを管理、登録、再デプロイ、および監視する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service を使用して、モデルをデプロイ、管理、および監視して、継続的に向上させる方法を説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning service を使用してデプロイできます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 25f149ad4df43a7e5b443d6abd72be91072cb47f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250207"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Azure Machine Learning service でモデルを管理、デプロイ、および監視する

この記事では、Azure Machine Learning service を使用して、お使いのモデルをデプロイ、管理、および監視して、継続的に向上させる方法を説明します。 トレーニングしたモデルを、ローカル コンピューターまたは他のソースから Azure Machine Learning を使用してデプロイできます。 

次の図は、完全なデプロイ ワークフローを示しています。[ ![Azure Machine Learning のデプロイ ワークフロー](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

デプロイ ワークフローには次の手順が含まれます。
1. **モデルを登録する**。お使いの Azure Machine Learning service ワークスペースでホストされているレジストリに登録します
1. **イメージを登録する**。このイメージにより、ポータブル コンテナー内で、モデルと、スコアリング スクリプトおよび依存関係がペアリングされます 
1. **デプロイする**。イメージを Web サービスとしてクラウドまたはエッジ デバイスにデプロイします
1. **データを監視して収集する**

各手順を個別に、または 1 つのデプロイ コマンドの一部として実行できます。 さらに、次の図に示すように、デプロイを **CI/CD ワークフロー**に統合することもできます。

[ !["Azure Machine Learning の継続的インテグレーション/継続的デプロイ (CI/CD) サイクル"](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>手順 1:モデルの登録

モデル レジストリにより、Azure Machine Learning service ワークスペース内のすべてのモデルが追跡されます。
モデルは、名前とバージョンによって識別されます。 モデルを登録するたびに、既存のモデルと同じ名前で登録され、レジストリによってバージョンがインクリメントされます。 モデルの検索時に使用できる追加のメタデータ タグを、登録中に指定することもできます。

イメージによって使用されているモデルを削除することはできません。

## <a name="step-2-register-image"></a>手順 2:イメージを登録する

イメージによって信頼性の高いモデル デプロイと、そのモデルの使用に必要なすべてのコンポーネントが準備されます。 イメージには次の項目が含まれます。

* モデル
* スコアリング エンジン
* スコアリング ファイルまたはアプリケーション
* モデルをスコアを付けるときに必要なすべての依存関係

イメージには、ログ記録と監視用の SDK コンポーネントを含めることもできます。 SDK ログ データは、モデルの入出力を含め、モデルの微調整や再トレーニングに使用できます。

Azure Machine Learning では、最もよく使用されているフレームワークがサポートされていますが、一般的には、pip でインストールできる任意のフレームワークが動作します。

ワークスペースの作成時に、そのワークスペースで使用される他の Azure リソースもいくつか作成されています。
イメージの作成に使用されるオブジェクトはすべて、お使いのワークスペースの Azure ストレージ アカウントに格納されています。 イメージは Azure Container Registry で作成され、格納されています。 イメージを作成するときに、追加のメタデータ タグを指定できます。このタグもイメージ レジストリによって格納されます。このタグにクエリを実行すると、ご自身のイメージを検索できます。

## <a name="step-3-deploy-image"></a>手順 3:イメージをデプロイする

登録済みのイメージを、クラウドまたはエッジ デバイスにデプロイできます。 デプロイ プロセスでは、お使いのモデルの監視、負荷分散、および自動スケーリングに必要なリソースがすべて作成されます。 デプロイされたサービスへのアクセスを証明書ベースの認証で保護するには、デプロイ中にセキュリティ アセットを指定します。 既存のデプロイをアップグレードして、新しいイメージを使用することもできます。

Web サービスのデプロイも検索可能です。 たとえば、特定のモデルまたはイメージのすべてのデプロイを検索できます。

[ ![ターゲットの推論](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

ご自身のイメージを、クラウド内の次の[デプロイ ターゲット](how-to-deploy-and-where.md)にデプロイできます。

* Azure Container Instances
* Azure Kubernetes Service
* Azure FPGA マシン
* Azure IoT Edge デバイス

ご自身のサービスがデプロイされると、推論要求が自動的に負荷分散され、クラスターは、オンデマンドでスパイクに対応できるようにスケーリングされます。 お使いのワークスペースに関連付けられている Azure Application Insights サービスに、[お使いのサービスに関するテレメトリをキャプチャできます](how-to-enable-app-insights.md)。

## <a name="step-4-monitor-models-and-collect-data"></a>手順 4:モデルを監視して、データを収集する

お使いのモデルからの入力、出力、およびその他の関連データを監視できるように、モデルのログ記録とデータ キャプチャ用の SDK を利用できます。 データは、ご自身のワークスペースの Azure Storage アカウントに BLOB として格納されています。

お使いのモデルで SDK を使用するには、その SDK をスコアリング スクリプトまたはアプリケーションにインポートします。 その後、SDK を使用して、パラメーター、結果、入力の詳細などのデータをログに記録します。

イメージをデプロイするたびに[モデル データ収集を有効にする](how-to-enable-data-collection.md)ように設定すると、ご自身の個人 BLOB ストアに対する資格情報など、データのキャプチャに必要な詳細情報が自動的にプロビジョニングされます。

> [!Important]
> モデルから収集したデータは Microsoft には表示されません。 データは、ご自身のワークスペースの Azure ストレージ アカウントに直接送信されます。

## <a name="next-steps"></a>次の手順

Azure Machine Learning サービスを使用して[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
