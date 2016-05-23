<properties
   pageTitle="ポータルで AD のアプリケーションとサービス プリンシパルを作成する | Microsoft Azure"
   description="Azure リソース マネージャーでロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しい Active Directory のアプリケーションとサービス プリンシパルを作成する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="tomfitz"/>

# ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する

## 概要
リソースへのアクセスまたは変更が必要な自動プロセスまたはアプリケーションがある場合、クラシック ポータルを使用して Active Directory アプリケーションを作成する方法があります。アプリケーションの実行には、アプリケーション独自の ID、またはアプリケーションにサインインしたユーザーの ID を使用できます。アプリケーションを認証するこれら 2 つの方法は、対話型 (ユーザーのサインイン) と非対話型 (アプリケーションが用意した独自の資格情報) と呼ばれます。非対話型モードの場合、正しいアクセス許可を持つロールをアプリケーションの ID に割り当てる必要があります。アプリケーションが自動実行されている場合は、非対話型の認証を使用する必要があります。

このトピックでは、クラシック ポータルを使用して、新しいアプリケーションを作成する方法について説明します。現時点では、新しい Active Directory アプリケーションを作成するには、クラシック ポータルを使用する必要があります。ポータルを使用して、アプリケーションをロールに割り当てることができます。

これらの手順は、Azure Powershell または Azure CLI を介して実行することもできます。サービス プリンシパルで PowerShell または CLI を使用する方法については、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」を参照してください。

## 概念
1. Azure Active Directory (AAD) - クラウド用に構築された ID およびアクセス管理サービス。詳細については、「[Azure Active Directory とは](active-directory/active-directory-whatis.md)」をご覧ください。
2. AD アプリケーション - アプリケーションを特定する Active Directory 内のディレクトリ レコード。 
3. サービス プリンシパル - アクセス制御ロールの適用先アプリケーションのインスタンス。

アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](active-directory/active-directory-authentication-scenarios.md)」をご覧ください。


## アプリケーションを作成する

対話型アプリケーションと非対話型アプリケーションに合わせて Active Directory アプリケーションを作成し、構成する必要があります。

1. [従来のポータル](https://manage.windowsazure.com/)によって Azure アカウントにログインします。

2. 左側のペインで **[Active Directory]** を選択します。

     ![Active Directory の選択][1]
     
3. 新しいアプリケーションを作成するために使用するディレクトリを選択します。サブスクリプションのリソースについては、使用しているサブスクリプションと同じディレクトリのサービス プリンシパルにのみアクセスを割り当てることができます。通常、サブスクリプションが存在するディレクトリにアプリケーションを作成します。

     ![ディレクトリの選択][2]
     
    サブスクリプションのディレクトリを探す場合は、**[設定]** を選択し、ディレクトリ名を検索します。
   
     ![既定のディレクトリの検索](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. ディレクトリ内のアプリケーションを表示するには、**[アプリケーション]** をクリックします。

     ![アプリケーションの表示][11]

4. そのディレクトリにアプリケーションを作成したことがない場合、次の図のようなものが表示されます。**[アプリケーションの追加]** をクリックします。

     ![アプリケーションの追加][6]

     または下のペインの **[追加]** をクリックします。

     ![追加][12]

5. 作成するアプリケーションの種類を選択します。このチュートリアルでは、ギャラリーのアプリケーションは使用しません。

     ![新規アプリケーション][10]

6. アプリケーションの名前を入力し、使用するアプリケーションの種類を選択します。作成するアプリケーションの種類を選択します。このチュートリアルでは、**[Web アプリケーションや Web API]** を選択して作成し、[次へ] をクリックします。

     ![アプリケーションの名前指定][9]

7. アプリのプロパティを入力します。**[サインオン URL]** には、アプリケーションについて説明する web サイトの URI を指定します。Web サイトの存在は検証されません。**[アプリケーション ID/URI]** には、アプリケーションを識別する URI を指定します。エンドポイントの一意性や存在は検証されません。アプリケーションの種類で **[ネイティブ クライアント アプリケーション]** を選択した場合は、**[リダイレクト URI]** の値を指定します。**[完了]** をクリックして、AAD アプリケーションを作成します。

     ![アプリケーションのプロパティ][4]

これでアプリケーションが作成されます。

## クライアント ID とテナント ID の取得

アプリケーションにプログラムでアクセスするときは、アプリケーションの ID が必要です。**[構成]** タブをクリックし、**クライアント ID** をコピーします。
  
   ![クライアント ID][5]

場合によっては、認証要求とともにテナント ID を渡す必要があります。Web Apps および Web API Apps の場合、テナント ID を取得するには、画面の下部にある **[エンドポイントの表示]** を選択して、次のように ID を取得します。

   ![テナント ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

ネイティブ クライアント アプリケーションではエンドポイントを使用できません。代わりに、次の PowerShell を使ってテナント ID を取得できます。

    Get-AzureRmSubscription

または、次の Azure CLI を使います。

    azure account show --json

## 認証キーを作成する

独自の資格情報でアプリケーションを実行する場合、そのアプリケーションのキーを作成する必要があります。

1. **[構成]** タブをクリックして、 アプリケーションのパスワードを構成します。

     ![アプリケーションの構成][3]

2. 下にスクロールして **キー** セクションを表示し、パスワードを有効にする期間を選択します。

     ![キー][7]

3. **[保存]** を選択してキーを作成します。

     ![保存][13]

     保存されたキーが表示され、それをコピーすることができます。キーは後からは取得できないため、今すぐコピーしてください。

     ![保存されたキー][8]

これで、アプリケーションの準備が完了し、サービス プリンシパルがテナントに作成されました。サービス プリンシパルとしてサインインするときには、以下を使用してください。

* **クライアント ID** - ユーザー名として。
* **キー** - パスワードとして。

## 委任されたアクセス許可を設定する

アプリケーションから、サインイン済みユーザーの代理でリソースにアクセスする場合、そのアプリケーションに対して、他のアプリケーションにアクセスできる委任されたアクセス許可を付与する必要があります。この設定は、**[構成]** タブの **[他のアプリケーションに対するアクセス許可]** で行います。Azure Active Directory の既定では、委任されたアクセス許可は有効です。この委任されたアクセス許可は変更しないでください。

1. **[アプリケーションの追加]** をクリックします。

2. 一覧から **[Azure Service Management API]** を選択します。

      ![アプリケーションの選択](./media/resource-group-create-service-principal-portal/select-app.png)

3. **Access Azure Service Management (プレビュー)** の委任されたアクセス許可を Service Management API に追加します。

       ![アクセス許可の選択](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 変更を保存します。

## マルチテナント アプリケーションを構成する

他の Azure Active Directory のユーザーがアプリケーションに同意してサインインできるようにするには、マルチテナント機能を有効にする必要があります。**[構成]** タブで、**[アプリケーションはマルチテナントです]** を **[はい]** に設定します。

![マルチテナント](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## アプリケーションをロールに割り当てる

サインインしたユーザーの ID でアプリケーションが実行されていない場合、アプリケーションをロールに割り当てて、アクションを実行するアクセス許可を付与する必要があります。アプリケーションをロールに割り当てるには、クラシック ポータルから [Azure ポータル](https://portal.azure.com)に切り替えます。アプリケーションをどのロールにどのようなスコープで追加するかを決める必要があります。利用可能なロールについては、「[RBAC: 組み込みのロール](./active-directory/role-based-access-built-in-roles.md)」を参照してください。スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。アクセス許可は、スコープの下位レベルに継承されます (たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます)。

1. ポータルで、アプリケーションを割り当てるスコープのレベルに移動します。このトピックでは、リソース グループに移動し、リソース グループ ブレードで **[アクセス]** アイコンを選択します。

     ![ユーザーの選択](./media/resource-group-create-service-principal-portal/select-users.png)

2. **[追加]** を選択します。

     ![select add](./media/resource-group-create-service-principal-portal/select-add.png)

3. **[閲覧者]** ロール (または、アプリケーションを割り当てる任意のロール) を選択します。

     ![select role](./media/resource-group-create-service-principal-portal/select-role.png)

4. ロールに追加できるユーザーの一覧が初めて表示される場合、アプリケーションは表示されません。グループとユーザーのみが表示されます。

     ![show users](./media/resource-group-create-service-principal-portal/show-users.png)

5. アプリケーションを見つけるには、検索する必要があります。アプリケーションの名前を入力し始めると、使用可能なオプションの一覧が変更されます。アプリケーションが一覧に表示されたら、選択します。

     ![assign to role](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. **[OK]** を選択して、ロールの割り当てを完了します。リソース グループのロールに割り当てられたユーザーの一覧にアプリケーションが表示されるようになりました。

     ![show](./media/resource-group-create-service-principal-portal/show-app.png)

ポータルを使用してユーザーやアプリケーションをロールに割り当てる方法の詳細については、「[Azure 管理ポータルを使用したアクセス権の管理](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)」を参照してください。

## アクセス トークンをコードで取得する

.NET を使用している場合は、次のコードを使用し、アプリケーションのアクセス トークンを取得できます。

まず、Visual Studio プロジェクトに Active Directory 認証ライブラリをインストールする必要があります。これを行う最も簡単な方法は、NuGet パッケージを使用することです。パッケージ マネージャー コンソールを開き、次のコマンドを入力します。

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

ID とシークレットを使用してサインインするには、次の方法でトークンを取得します。

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

ユーザーの代理でサインインするには、次の方法でトークンを取得します。

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

トークンを要求ヘッダーで渡すには、次のコードを使用します。

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## 次のステップ

- セキュリティ ポリシーを指定する方法については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。  
- これらの手順のビデオ デモについては、[Azure Active Directory を使用した Azure リソースのプログラムによる管理の有効化](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)に関するビデオを参照してください。
- 認証に証明書を利用する方法を含む、Azure PowerShell または Azure CLI で Active Directory のアプリケーションとサービス プリンシパルを操作する方法については、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」を参照してください。
- Azure リソース マネージャーを使用したセキュリティの実装のガイダンスについては、「[Azure Resource Manager のセキュリティに関する考慮事項](best-practices-resource-manager-security.md)」を参照してください。


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0511_2016-->