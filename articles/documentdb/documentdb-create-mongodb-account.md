---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>MongoDB API を使用する Azure Cosmos DB アカウントの作成
MongoDB 向けに作成されたアプリのデータ ストアとして、Azure Cosmos DB データベースを使用できるようになりました。 この機能を使用するには、Azure アカウントと Azure Cosmos DB アカウントが必要です。 このチュートリアルでは、MongoDB アプリで使用するための Azure Cosmos DB アカウントを作成するプロセスについて説明します。 

MongoDB をサポートする Azure Cosmos DB アカウントは、Azure Portal を使用するか、Azure CLI と Azure Resource Manager テンプレートを使用して作成できます。 この記事では、Azure Portal を使用して、MongoDB をサポートする Azure Cosmos DB アカウントを作成する方法について説明します。 Azure CLI と Azure Resource Manager を使用してアカウントを作成する方法については、[Azure CLI 2.0 を使用した Azure Cosmos DB アカウント管理の自動化](documentdb-automation-resource-manager-cli.md)に関する記事をご覧ください。

## <a name="prerequisite"></a>前提条件
Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントの作成

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーションで、**[Azure Cosmos DB]** をクリックします。

    ![DocumentDB NoSQL エントリが強調表示された、ポータルの左側のナビゲーションのスクリーン ショット](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. または、**[その他のサービス >]** をクリックし、上部の検索バーに「**DocumentDB**」と入力して、**[Azure Cosmos DB]** をクリックします。

    ![DocumentDB NoSQL エントリを検索するための [その他のサービス] ブレードのスクリーン ショット](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. **[Azure Cosmos DB]** ブレードの上部で、操作バーの **[+ 追加]** をクリックします。

    ![Cosmos DB リソース ブレードの [追加] ボタンのスクリーンショット](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. **[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]** ブレードで、アカウントに必要な構成を指定します。

   ![MongoDB のプロトコルに対応する新しい Azure Cosmos DB ブレードのスクリーンショット](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - **[ID]** ボックスに、アカウントを識別する名前を入力します。  **ID** が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。 この **ID** の値は、URI 内のホスト名になります。 **ID** に含めることができるのは英小文字、数字、および "-" のみで、文字数は 3 ～ 50 文字にする必要があります。 選択したエンドポイント名に *documents.azure.com* が追加され、これがアカウントのエンドポイントになります。

    - **[API]** で **[MongoDB]** を選択します。 これにより、Azure Cosmos DB データベースとのやり取りに使用する通信 API が指定されます。

    - **[サブスクリプション]**で、アカウントに使用する Azure サブスクリプションを選択します。 アカウントのサブスクリプションが 1 つである場合は、既定ではそのアカウントが選択されます。

    - **[リソース グループ]**で、アカウントのリソース グループを選択または作成します。  既定では、Azure サブスクリプションの既存のリソース グループが選択されます。  ただし、アカウントを追加する新しいリソース グループの作成を選択することもできます。 詳細については、「 [Azure ポータルを使用した Azure リソースの管理](../azure-portal/resource-group-portal.md)」を参照してください。

    - **[場所]** を使用して、アカウントのホストとなる地理的場所を指定します。

6. 新しいアカウント オプションを構成したら、 **[作成]**をクリックします。  アカウントの作成には数分かかる場合があります。

   通知ハブから進行状況を監視できます。  

   ![Azure Cosmos DB アカウントを作成中であることを示す通知ハブのスクリーンショット](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. 新しいアカウントにアクセスするには、左側のメニューの **[Azure Cosmos DB]** をクリックします。 通常の DocumentDB と Mongo プロトコル サポート アカウントを持つ DocumentDB が含まれるリストで、新しいアカウントの名前をクリックします。
8. Azure Cosmos DB アカウントを MongoDB アプリで使用する準備ができました。

   ![Screen shot of the default account blade](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>次のステップ
* MongoDB のプロトコル対応の DocumentDB アカウントに [接続](documentdb-connect-mongodb-account.md) する方法を確認します。

