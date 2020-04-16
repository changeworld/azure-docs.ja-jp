---
title: Azure Data Explorer で Azure AD アプリケーションを作成する
description: Azure Data Explorer で Azure AD アプリケーションを作成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80576798"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Azure Data Explorer で Azure Active Directory アプリケーションの登録を作成する

Azure Active Directory (Azure AD) アプリケーション認証は、ユーザーが存在せずに Azure Data Explorer にアクセスする必要がある、無人サービスやスケジュールされたフローなどのアプリケーションに使用されます。 Web アプリなどのアプリケーションを使用して Azure Data Explorer データベースに接続している場合は、サービス プリンシパル認証を使用して認証を行う必要があります。 この記事では、Azure AD サービス プリンシパルを作成して登録し、Azure Data Explorer データベースへのアクセスを承認する方法について詳しく説明します。

## <a name="create-azure-ad-application-registration"></a>Azure AD アプリケーションの登録を作成する

Azure AD アプリケーションの認証では、Azure AD でアプリケーションを作成および登録する必要があります。 サービス プリンシパルは、アプリケーションの登録が Azure AD テナントに作成されたときに自動的に作成されます。 

1. [Azure portal](https://portal.azure.com) にログインし、[`Azure Active Directory`] ブレードを開きます。

    ![ポータル メニューの [Azure Active Directory] を選択する](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. **[アプリの登録]** ブレードを選択し、 **[新しい登録]** を選択します。

    ![新しいアプリの登録を開始する](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. 次の情報を入力します。 

    * **名前** 
    * **サポートされているアカウントの種類**
    * **[リダイレクト URI]**  >  **[Web]**
        > [!IMPORTANT] 
        > アプリケーションの種類は **[Web]** にする必要があります。 URI は省略可能であり、ここでは空白のままにします。
    * **[登録]** を選択します

    ![新しいアプリ登録を登録する](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. **[概要]** ブレードを選択し、 **[アプリケーション ID]** をコピーします。

    > [!NOTE]
    > サービス プリンシパルのデータベースへのアクセスを承認するためにアプリケーション ID が必要です。

    ![アプリの登録 ID をコピーする](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. **[証明書とシークレット]** ブレードで、 **[新しいクライアント シークレット]** を選択します。

    ![クライアント シークレットの作成を開始する](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > この記事では、アプリケーションの資格情報にクライアント シークレットを使用する方法について説明します。  X509 証明書を使用してアプリケーションを認証することもできます。 **[証明書のアップロード]** を選択し、指示に従って証明書の公開部分をアップロードします。

1. 説明と有効期限を入力し、 **[追加]** を選択します

    ![クライアント シークレットのパラメーターを入力](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. キー値をコピーします。

    > [!NOTE]
    > このページから移動すると、キー値にアクセスできなくなります。  データベースに対してクライアント資格情報を構成するためにキーが必要になります。

    ![クライアント シークレットのキー値をコピー](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

アプリケーションが作成されました。 次のプログラム例のように、承認された Azure Data Explorer リソースへのアクセスのみが必要な場合は、次のセクションをスキップします。 委任されたアクセス許可については、「[アプリケーション登録の委任されたアクセス許可を構成する](#configure-delegated-permissions-for-the-application-registration)」を参照してください。

## <a name="configure-delegated-permissions-for-the-application-registration"></a>アプリケーション登録の委任されたアクセス許可を構成する

アプリケーションが呼び出し元のユーザーの資格情報を使用して Azure Data Explorer にアクセスする必要がある場合は、アプリケーション登録の委任されたアクセス許可を構成します。 たとえば、Azure Data Explorer にアクセスするための Web API を構築しており、API の "*呼び出し元*" ユーザーの資格情報を使用して認証したい場合などです。  

1. **[API のアクセス許可]** ブレードで、 **[アクセス許可の追加]** を選択します。
1. **[所属する組織で使用している API]** を選択します。 **[Azure Data Explorer]** を検索して、選択します。

    ![Azure Data Explorer API のアクセス許可を追加](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. **[委任されたアクセス許可]** で、 **[user_impersonation]** ボックスを選択し、 **[アクセス許可の追加]** を選択します。

    ![ユーザー偽装の委任されたアクセス許可を選択](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>サービス プリンシパルに Azure Data Explorer データベースへのアクセスを許可する

サービス プリンシパル アプリケーションの登録が作成されたので、対応するサービス プリンシパルに Azure Data Explorer データベースへのアクセス権を付与する必要があります。 

1. [Web UI](https://dataexplorer.azure.com/) で、データベースに接続し、クエリ ウィンドウを開きます。

1. たとえば、次のコマンドを実行します。

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    次に例を示します。
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    最後のパラメーターは、データベースに関連付けられているロールに対してクエリを実行するときに、メモとして表示される文字列です。
    
    > [!NOTE]
    > アプリケーションの登録を作成した後、Azure Data Explorer がそれを参照できるようになるまでに、数分間の遅延が発生する場合があります。 コマンドを実行したときに、アプリケーションが見つからないというエラーが発生した場合は、しばらく待ってから再試行してください。

詳細については、「[セキュリティ ロールの管理](/azure/kusto/management/security-roles)」と「[インジェストのアクセス許可](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)」を参照してください。  

## <a name="using-application-credentials-to-access-a-database"></a>アプリケーションの資格情報を使用したデータベースへのアクセス

アプリケーションの資格情報を使用して、[Azure Data Explorer クライアントライブラリ](/azure/kusto/api/netfx/about-kusto-data.md)を使用してプログラムでデータベースにアクセスします。

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > 先ほど作成したアプリケーション登録 (サービス プリンシパル) のアプリケーション ID とキーを指定します。

詳細については、[Azure Data Explorer のアクセスのための Azure AD での認証](/azure/kusto/management/access-control/how-to-authenticate-with-aad)と [.NET Core Web アプリ での Azure Key Vault の使用](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)に関する記事を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="invalid-resource-error"></a>無効なリソース エラー

アプリケーションを使用して Azure Data Explorer アクセス用のユーザーまたはアプリケーションを認証する場合は、Azure Data Explorer サービス アプリケーションの委任されたアクセス許可を設定する必要があります。 Azure Data Explorer アクセス用にアプリケーションがユーザーまたはアプリケーションを認証できることを宣言します。 そうしないと、認証が試行されたときに、次のようなエラーが発生します。

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

[Azure Data Explorer サービス アプリケーションに対して委任されたアクセス許可を設定する](#configure-delegated-permissions-for-the-application-registration)手順に従う必要があります。

### <a name="enable-user-consent-error"></a>ユーザーの同意の有効化に関するエラー

Azure AD テナント管理者は、テナントのユーザーがアプリケーションに同意しないようにするポリシーを施行することがあります。 この状況では、ユーザーがアプリケーションにログインしようとすると、次のようなエラーが発生します。

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Azure AD 管理者に連絡して、テナント内のすべてのユーザーに同意を付与するか、特定のアプリケーションに対するユーザーの同意を有効にする必要があります。

## <a name="next-steps"></a>次のステップ

* サポートされている接続文字列の一覧について、「[Kusto 接続文字列](/azure/kusto/api/connection-strings/kusto.md)」を参照してください。
