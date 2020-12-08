---
title: チュートリアル:Azure Synapse における Cognitive Services の前提条件
description: Azure Synapse で Cognitive Services を使用するための前提条件を構成する方法を示すチュートリアル
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: cf76a3d09da83693791873feb9e6ab03c432e47f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464399"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>チュートリアル:Azure Synapse で Cognitive Services を使用するための前提条件

このチュートリアルでは、Azure Synapse で Cognitive Services を安全に利用するための前提条件を設定する方法について説明します。

このチュートリアルの内容:
> [!div class="checklist"]
> - Cognitive Services リソースを作成する (例: Text Analytics または Anomaly Detector)。
> - Cognitive Services リソースに対する認証キーをシークレットとして Azure Key Vault に格納し、Azure Synapse ワークスペースのアクセスを構成する。
> - Azure Synapse Analytics ワークスペースで Azure Key Vault のリンクされたサービスを作成する。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- ADLS Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する ADLS Gen2 ファイル システムの **ストレージ BLOB データ共同作成者** である必要があります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースを作成する

[Azure Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492) には、さまざまな種類のサービスが含まれています。 Synapse のチュートリアルで使用されている例を次に示します。

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector リソースを作成する
Azure portal で [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) を作成します。

![Anomaly Detector の作成](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Text Analytics リソースを作成する
Azure portal で [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) リソースを作成します。

![Text Analytics の作成](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>キー コンテナーを作成してシークレットとアクセスを構成する

1. Azure portal で[キー コンテナー](https://ms.portal.azure.com/#create/Microsoft.KeyVault)を作成します。
2. **[Key Vault]、[アクセス ポリシー]** の順に移動し、[Azure Synapse ワークスペースの MSI](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-identity) に、Azure Key Vault からシークレットを読み取るためのアクセス許可を付与します。

>ポリシーの変更を必ず保存します。 これは見逃されやすい手順です。

![アクセス ポリシーの追加](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Cognitive Service リソースに移動します。たとえば、 **[Anomaly Detector]、[Keys and Endpoint]\(キーとエンドポイント\)** に移動し、2 つのキーのいずれかをクリップボードにコピーします。

4. **[Key Vault]、[シークレット]** の順に移動して、新しいシークレットを作成します。 シークレットの名前を指定し、前の手順のキーを [値] フィールドに貼り付けます。 最後に、 **[作成]** をクリックします。

![シークレットの作成](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> このシークレット名を覚えておくか、書き留めておいてください。 後で Azure Synapse Studio から Cognitive Services に接続するときに使用します。

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Azure Synapse で Azure Key Vault のリンクされたサービスを作成する

1. Azure Synapse Studio でワークスペースを開きます。 **[管理]、[リンクされたサービス]** の順に移動します。 先ほど作成したキー コンテナーを参照する "Azure Key Vault" のリンクされたサービスを作成します。 次に、[テスト接続] ボタンをクリックし、緑色になっているかどうかを確かめて、接続を確認します。 問題がなければ、まず [作成] をクリックしてから、[すべて公開] をクリックして変更を保存します。
![リンクされたサービス](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

これで、Azure Synapse Studio で Azure Cognitive Services エクスペリエンスを使用するためのチュートリアルのいずれかに進む準備ができました。

## <a name="next-steps"></a>次の手順

- [チュートリアル: Azure Cognitive Services を使用した感情分析](tutorial-cognitive-services-sentiment.md)
- [チュートリアル: Azure Cognitive Services を使用した異常検出](tutorial-cognitive-services-sentiment.md)
- [チュートリアル: Azure Synapse 専用 SQL プールでの機械学習モデルのスコアリング](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)