---
title: クイック スタート:Azure Machine Learning ワークスペースをリンクする
description: Synapse ワークスペースを Azure Machine Learning ワークスペースにリンクする
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: 7d6b9a81f5e5e948704b4597a7283cd976b124c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847202"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>クイック スタート:Synapse で Azure Machine Learning のリンクされたサービスを新規作成する

このクイックスタートでは、Azure Synapse Analytics ワークスペースを Azure Machine Learning ワークスペースにリンクします。 これらのワークスペースをリンクすると、Synapse のさまざまなエクスペリエンスから Azure Machine Learning を利用できるようになります。

たとえば、Azure Machine Learning ワークスペースにリンクすると、次のようなエクスペリエンスが可能になります。

- Azure Machine Learning パイプラインを Synapse パイプラインのステップとして実行する。 詳細については、[Azure Machine Learning パイプラインの実行](../../data-factory/transform-data-machine-learning-service.md)に関する記事を参照してください。

- Azure Machine Learning モデル レジストリから機械学習モデルをコピーすることで、予測によってデータを強化し、Synapse SQL プールでモデルにスコアを付けます。 詳細については、「[チュートリアル: Synapse SQL プール向けの機械学習モデル スコアリング ウィザード](tutorial-sql-pool-model-scoring-wizard.md)」を参照してください。

## <a name="two-types-of-authentication"></a>2 種類の認証
Azure Synapse で Azure Machine Learning リンク サービスを作成する際に使用できる ID には 2 種類あります。
* Synapse ワークスペースのマネージド ID
* サービス プリンシパル

次のセクションでは、これら 2 種類の認証を使用して Azure Machine Learning リンク サービスを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)。
- ADLS Gen2 ストレージ アカウントが既定のストレージとして構成されている [Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する ADLS Gen2 ファイル システムの **ストレージ BLOB データ共同作成者** である必要があります。
- [Azure Machine Learning ワークスペース](../../machine-learning/how-to-manage-workspace.md)。
- サービス プリンシパルの使用を選択した場合、リンク サービスの作成に使用できるサービス プリンシパルとシークレットを作成するには、アクセス許可 (またはアクセス許可を持つユーザーからの要求) が必要です。 このサービス プリンシパルには Azure Machine Learning ワークスペースの共同作成者ロールが割り当てられている必要があることに注意してください。
- [Azure ポータル](https://portal.azure.com/)

## <a name="create-a-linked-service-using-the-synapse-workspace-managed-identity"></a>Synapse ワークスペース マネージド ID を使用してリンク サービスを作成する

このセクションでは、[Azure Synapse ワークスペース マネージド ID](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) を使用して、Azure Synapse で Azure Machine Learning リンク サービスを作成する方法について説明します

### <a name="give-msi-permission-to-the-azure-ml-workspace"></a>Azure ML ワークスペースに MSI アクセス許可を付与する

1. Azure portal で Azure Machine Learning ワークスペース リソースに移動し、 **[アクセス制御]** を選択します

1. ロールの割り当てを作成し、Synapse ワークスペース マネージド サービス ID (MSI) を Azure Machine Learning ワークスペースの "*共同作成者*" として追加します。 これは、Azure Machine Learning ワークスペースが属しているリソース グループの所有者である必要があることに注意してください。 Synapse ワークスペース MSI が見つからない場合は、Synapse ワークスペースの名前を検索してください。

### <a name="create-an-azure-ml-linked-service"></a>Azure ML リンク サービスを作成する

1. 新しい Azure Machine Learning のリンクされたサービスを作成する Synapse ワークスペースで、 **[管理]**  >  **[リンクされたサービス]** に移動し、タイプを "Azure Machine Learning" として新しいリンクされたサービスを作成します。

   ![リンクされたサービスの作成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. 以下をフォームに入力します。

   - リンク先の Azure Machine Learning ワークスペースの詳細を指定します。 たとえば、サブスクリプションとワークスペース名の詳細です。
   
   - [認証方法] として **[マネージド ID]** を選択します
  
3. **[接続のテスト]** をクリックして、構成が正しいかどうかを確認します。 接続テストが成功した場合は、 **[保存]** をクリックします。

   接続テストに失敗した場合は、この Azure Machine Learning ワークスペースにアクセスするためのアクセス許可が Azure Synapse ワークスペース MSI にあることを確認して再試行します。

## <a name="create-a-linked-service-using-a-service-principal"></a>サービス プリンシパルを使用してリンク サービスを作成する

このセクションでは、サービス プリンシパルを使用して Azure Machine Learning リンク サービスを作成する方法について説明します。

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

この手順では、新しいサービス プリンシパルを作成します。 既存のサービス プリンシパルを使用する場合は、次の手順をスキップできます。

1. Azure portal を開きます。 

1. **[Azure Active Directory]**  ->  **[アプリの登録]** に移動します。

1. **[新規登録]** をクリックします。 次に、指示に従って、新しいアプリケーションを登録します。

1. アプリケーションが登録されたら、アプリケーションのシークレットを生成します。 **[アプリケーション]**  ->  **[証明書およびシークレット]** の順に移動します。 **[クライアント シークレットを追加]** をクリックして、シークレットを生成します。 シークレットは安全に保管してください。これは後で使用します。

   ![シークレットの生成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. アプリケーション用にサービス プリンシパルを作成します。 **[アプリケーション]**  ->  **[該当]** の順に移動し、 **[サービス プリンシパルの作成]** をクリックします。 場合によっては、このサービス プリンシパルは自動的に作成されます。

   ![サービス プリンシパルの作成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. サービス プリンシパルを Azure Machine Learning ワークスペースの "共同作成者" として追加します。 これは、Azure Machine Learning ワークスペースが属しているリソース グループの所有者である必要があることに注意してください。

   ![共同作成者ロールの割り当て](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

### <a name="create-an-azure-ml-linked-service"></a>Azure ML リンク サービスを作成する

1. 新しい Azure Machine Learning のリンクされたサービスを作成する Synapse ワークスペースで、 **[管理]**  ->  **[リンクされたサービス]** に移動し、タイプを "Azure Machine Learning" として新しいリンクされたサービスを作成します。

   ![リンクされたサービスの作成](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. 以下をフォームに入力します。

   - リンク先の Azure Machine Learning ワークスペースの詳細を指定します。 たとえば、サブスクリプションとワークスペース名の詳細です。

   - [認証方法] として **[サービス プリンシパル]** を選択します。

   - サービス プリンシパル ID: これはアプリケーションの **アプリケーション (クライアント) ID** です。

   > [!NOTE]
   > この ID はアプリケーションの名前ではありません。 この ID は、アプリケーションの ID の概要ページで確認できます。 これは、"81707eac-ab38-406u-8f6c-10ce76a568d5" のような長い文字列になります。

   - サービス プリンシパルのキー: 前のセクションで生成したシークレットです。

3. **[接続のテスト]** をクリックして、構成が正しいかどうかを確認します。 接続テストが成功した場合は、 **[保存]** をクリックします。

   接続テストが失敗した場合は、サービス プリンシパル ID とシークレットが正しいことを確認してから、もう一度やり直してください。

## <a name="next-steps"></a>次の手順

- [チュートリアル: 機械学習モデル スコアリング ウィザード - 専用 SQL プール](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics の機械学習機能](what-is-machine-learning.md)
