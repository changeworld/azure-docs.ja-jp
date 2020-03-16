---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305046"
---
## <a name="configure-the-data-source"></a>データ ソースの構成

Azure Data Explorer をダッシュボード ツールのデータ ソースとして構成するには、次の手順を実行します。 このセクションでは、これらの手順について詳しく説明します。

1. Azure Active Directory (Azure AD) サービス プリンシパルを作成します。 このサービス プリンシパルは、Azure Data Explorer サービスにアクセスするためにダッシュボード ツールによって使用されます。

1. Azure Data Explorer データベースの "*閲覧者*" ロールに Azure AD サービス プリンシパルを追加します。

1. Azure AD サービス プリンシパルの情報に基づいてダッシュボード ツールの接続プロパティを指定し、接続をテストします。

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

サービス プリンシパルは、[Azure portal](#azure-portal) または [Azure CLI](#azure-cli) コマンド ライン エクスペリエンスを使用して作成できます。 どちらの方法を使用しても、作成後に、後の手順で使用する 4 つの接続プロパティの値を取得します。

#### <a name="azure-portal"></a>Azure portal

1. サービス プリンシパルを作成するには、[Azure portal のドキュメント](/azure/active-directory/develop/howto-create-service-principal-portal)の指示に従います。

    1. 「[アプリケーションをロールに割り当てる](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)」で、Azure Data Explorer クラスターに**閲覧者**ロールを割り当てます。

    1. 「[サインインするための値を取得する](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)」で、後の手順で使用する 3 つのプロパティ値 (**ディレクトリ ID** (テナント ID)、**アプリケーション ID**、**パスワード**) をコピーします。

1. Azure portal で **[サブスクリプション]** を選択し、サービス プリンシパルを作成したサブスクリプションの ID をコピーします。

    ![サブスクリプション ID - ポータル](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. サービス プリンシパルを作成する。 適切なスコープを設定し、ロールの種類として `reader` を設定します。

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    詳細については、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」をご覧ください。

1. このコマンドでは、次のような結果セットが返されます。 **appId**、**password**、**tenant** の 3 つのプロパティ値をコピーします。


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. サブスクリプションの一覧を取得します。

    ```azurecli
    az account list --output table
    ```

    適切なサブスクリプション ID をコピーします。

    ![サブスクリプション ID - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>サービス プリンシパルを閲覧者ロールに追加する

サービス プリンシパルを作成したら、Azure Data Explorer データベースの "*閲覧者*" ロールに追加します。 この作業は、Azure portal の **[アクセス許可]** で実行することも、 **[クエリ]** で管理コマンドを使用して実行することもできます。

#### <a name="azure-portal---permissions"></a>Azure portal - [アクセス許可]

1. Azure portal で、Azure Data Explorer クラスターに移動します。

1. **[概要]** セクションで、StormEvents サンプル データが含まれたデータベースを選択します。

    ![データベースの選択](media/data-explorer-configure-data-source/select-database.png)

1. **[アクセス許可]** を選択し、 **[追加]** を選択します。

    ![データベース権限](media/data-explorer-configure-data-source/database-permissions.png)

1. **[Add database permissions]\(データベース アクセス許可の追加\)** で、 **[閲覧者]** ロールを選択し、 **[Select principals]\(プリンシパルの選択\)** を選択します。

    ![データベースのアクセス許可を追加する](media/data-explorer-configure-data-source/add-permission.png)

1. 作成したサービス プリンシパルを検索します。 プリンシパルを選択し、 **[選択]** を選択します。

    ![Azure portal でアクセス許可を管理する](media/data-explorer-configure-data-source/new-principals.png)

1. **[保存]** を選択します。

    ![Azure portal でアクセス許可を管理する](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>管理コマンド - [クエリ]

1. Azure portal で、Azure Data Explorer クラスターに移動し、 **[クエリ]** を選択します。

    ![クエリ](media/data-explorer-configure-data-source/query.png)

1. クエリ ウィンドウで次のコマンドを実行します。 Azure portal または CLI で取得したアプリケーション ID とテナント ID を使用します。

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    このコマンドでは、次のような結果セットが返されます。 この例では、最初の行はデータベース内の既存のユーザーを示し、2 番目の行は先ほど追加したサービス プリンシパルを示しています。

    ![結果セット](media/data-explorer-configure-data-source/result-set.png)
