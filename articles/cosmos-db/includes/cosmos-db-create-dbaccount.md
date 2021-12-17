---
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 07/02/2021
ms.custom: include file
ms.openlocfilehash: f03bf23140131b34b3d97c0dda6c707984b5d5c1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802126"
---
1. Azure portal のメニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[Azure Cosmos DB]** を検索して選択します。

1. **[Azure Cosmos DB]** ページで、 **[作成]** を選択します。

1. **[Azure Cosmos DB アカウントの作成]** ページで、新しい Azure Cosmos アカウントの基本的な設定を入力します。

   |設定|値|説明 |
   |---|---|---|
   |サブスクリプション|サブスクリプション名|この Azure Cosmos アカウントに使用する Azure サブスクリプションを選択します。 |
   |リソース グループ|リソース グループ名|リソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの一意の名前を入力します。 |
   |アカウント名|一意の名前|自分の Azure Cosmos アカウントを識別するための名前を入力します。 指定した名前に *documents.azure.com* が付加されて URI が作成されるので、一意の名前を使用してください。<br><br>名前に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 長さは 3 文字から 44 文字でなければなりません。|
   |API|作成するアカウントの種類。|ドキュメント データベースを作成し、SQL 構文を使用してクエリを実行するには、 **[コア (SQL)]** を選択します。 <br><br>API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、5 種類の API が用意されています。ドキュメント データ用のコア (SQL) と MongoDB、グラフ データ用の Gremlin、Azure Table、Cassandra です。 現在は、API ごとに別のアカウントを作成する必要があります。 <br><br>[SQL API について詳しくは、こちらをご覧ください](../introduction.md)。|
   |場所|ユーザーに最も近いリージョン|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできるよう、お客様のユーザーに最も近い場所を使用します。|
   |容量モード|プロビジョニング スループットまたはサーバーレス|[プロビジョニング スループット](../set-throughput.md) モードでアカウントを作成するには、 **[Provisioned throughput]\(プロビジョニング スループット\)** を選択します。 [サーバーレス](../serverless.md) モードでアカウントを作成するには、 **[サーバーレス]** を選択します。|
   |Apply Azure Cosmos DB free tier discount (Azure Cosmos DB Free レベル割引を適用する)|**[適用]** または **[適用しない]**|Azure Cosmos DB Free レベルのアカウントでは、最初の 1000 RU/s と 25 GB のストレージを無料でご利用いただけます。 [Free レベル](https://azure.microsoft.com/pricing/details/cosmos-db/)の詳細を確認してください。|

   > [!NOTE]
   > Azure サブスクリプションにつき所有できる Free レベルの Azure Cosmos DB アカウントは 1 つまでです。また、アカウントの作成時にオプトインする必要があります。 Free レベルの割引を適用するオプションが表示されない場合は、サブスクリプション内の別のアカウントが Free レベルで既に有効になっていることを意味します。

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Azure Cosmos DB の新しいアカウント ページ":::

1. **[グローバル分散]** タブで、次の詳細を構成します。 このクイックスタートでは、既定値のままでかまいません。

   |設定|値|説明 |
   |---|---|---|
   |geo 冗長性|無効化|リージョンをペア リージョンとペアリングすることによる、アカウントでのグローバル配信を有効または無効にします。 アカウントには、後でさらにリージョンを追加できます。|
   |マルチリージョン書き込み|無効化|マルチリージョン書き込み機能を使用すると、世界中のデータベースとコンテナーで、プロビジョニングされたスループットを利用できます。|

   > [!NOTE]
   > **[Capacity mode]\(容量モード\)** として **[サーバーレス]** を選択した場合、以下のオプションは利用できません。
   > - Apply Free Tier Discount (Free レベルの割引の適用)
   > - geo 冗長
   > - マルチリージョン ライター

1. 必要に応じて、次のタブで追加の詳細を構成できます。

   * **[ネットワーク]** - [仮想ネットワークからのアクセス](../how-to-configure-vnet-service-endpoint.md)を構成します。
   * **[バックアップ ポリシー]** - [定期的](../configure-periodic-backup-restore.md)または [継続的](../provision-account-continuous-backup.md)のいずれかのバックアップ ポリシーを構成します。
   * **[暗号化]** - サービス マネージド キーまたは [カスタマー マネージド キー](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account)のいずれかを使用します。
   * **[タグ]** - タグは名前と値のペアで、同じタグを複数のリソースやリソース グループに適用することでリソースを分類したり、統合した請求を表示したりできるようにします。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

1. アカウントの設定を確認し、 **[作成]** を選択します。 アカウントの作成には数分かかります。 ポータル ページに "**デプロイが完了しました**" と表示されるまで待ちます。

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Azure portal の [通知] ペイン":::

1. **[リソースに移動]** を選択し、Azure Cosmos DB アカウント ページに移動します。 

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Azure Cosmos DB アカウント ページ":::