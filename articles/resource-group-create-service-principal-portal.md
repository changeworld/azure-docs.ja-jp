<properties
   pageTitle="ポータルで AD のアプリケーションとサービス プリンシパルを作成する | Microsoft Azure"
   description="Azure リソース マネージャーでロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しい Active Directory のアプリケーションとサービス プリンシパルを作成する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する

## 概要
自動化プロセスまたはアプリケーションでサブスクリプションのリソースにアクセスするか、リソースを変更する必要があるとき、ポータルを利用し、Active Directory アプリケーションを作成し、適切なアクセス許可を持つロールにそれを割り当てることができます。ポータルを使用して Active Directory アプリケーションを作成すると、実際、アプリケーションとサービス プリンシパルの両方が作成されます。アクセス許可を設定するとき、サービス プリンシパルを使用します。

このトピックでは、Azure ポータルを使用して、新しいアプリケーションとサービス プリンシパルを作成する方法について説明します。現時点では、新しい Active Directory アプリケーションを作成するには、Microsoft Azure ポータルを使用する必要があります。この機能は、今後のリリースで、Azure プレビュー ポータルに追加されます。プレビュー ポータルを使用し、アプリケーションをロールに割り当てることができます。これらの手順は、Azure Powershell または Azure CLI を介して実行することもできます。詳細については、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」を参照してください。

## 概念
1. Azure Active Directory (AAD) - クラウド用に構築された ID およびアクセス管理サービス。詳しくは、「[Azure Active Directory とは](active-directory/active-directory-whatis.md)」を参照してください。
2. サービス プリンシパル - ディレクトリ内のアプリケーションのインスタンス。
3. AD アプリケーション - AAD に対してアプリケーションを特定する ADD 内のディレクトリ レコード。 

アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](active-directory/active-directory-authentication-scenarios.md)」をご覧ください。


## アプリケーション オブジェクトとサービス プリンシパル オブジェクトを作成する

1. [ポータル](https://manage.windowsazure.com/)から Azure アカウントにログインします。

2. 左側のペインで **[Active Directory]** を選択します。

     ![Active Directory の選択][1]

3. 新しいアプリケーションを作成するために使用するディレクトリを選択します。

     ![ディレクトリの選択][2]

3. ディレクトリ内のアプリケーションを表示するには、**[アプリケーション]** をクリックします。

     ![アプリケーションの表示][11]

4. そのディレクトリにアプリケーションを作成したことがない場合、次の図のようなものが表示されます。**[アプリケーションの追加]** をクリックします。

     ![アプリケーションの追加][6]

     または下のペインの **[追加]** をクリックします。

     ![追加][12]

5. 作成するアプリケーションの種類を選択します。このチュートリアルでは、ギャラリーのアプリケーションは使用しません。

     ![新規アプリケーション][10]

6. アプリケーションの名前を入力し、使用するアプリケーションの種類を選択します。アプリケーションのサービス プリンシパルは Azure リソース マネージャーでの認証に使用するため、 **[Web アプリケーションや Web API]** の作成を選択して、[次へ] ボタンをクリックします。

     ![アプリケーションの名前指定][9]

7. アプリのプロパティを入力します。**[サインオン URL]** には、アプリケーションについて説明する web サイトの URI を指定します。Web サイトの存在は検証されません。**[アプリケーション ID/URI]** には、アプリケーションを識別する URI を指定します。エンドポイントの一意性や存在は検証されません。**[完了]** をクリックして、AAD アプリケーションを作成します。

     ![アプリケーションのプロパティ][4]

## アプリケーションの認証キーを作成する
ポータルでは、ユーザーのアプリケーションが選択されているはずです。

1. **[構成]** タブをクリックして、 アプリケーションのパスワードを構成します。

     ![アプリケーションの構成][3]

2. 下にスクロールして **キー** セクションを表示し、パスワードを有効にする期間を選択します。

     ![キー][7]

3. **[保存]** を選択してキーを作成します。

     ![保存][13]

     保存されたキーが表示され、それをコピーすることができます。キーは後からは取得できないため、今すぐコピーしてください。

     ![保存されたキー][8]

4. これで、サービス プリンシパルとして認証するためにそのキーを使用できます。サインインするためには、**キー**に加えて**クライアント ID** が必要です。**[クライアント ID]** に移動して、それをコピーしてください。
  
     ![クライアント ID][5]

5. 場合によっては、認証要求とともにテナント ID を渡す必要があります。テナント ID を取得するには、**[エンドポイントの表示]** を選択して、次のように ID を取得します。

     ![テナント ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

これで、アプリケーションの準備が完了し、サービス プリンシパルがテナントに作成されました。サービス プリンシパルとしてサインインするときには、以下を使用してください。

* **クライアント ID** - ユーザー名として。
* **キー** - パスワードとして。

## ロールにアプリケーションを割り当てる

アクションを実行するアクセス許可を付与するには、ロールにアプリケーションを割り当てる必要があります。[プレビュー ポータル](https://portal.azure.com)を使用し、ロールに Active Directory アプリケーションを正しいアクセス許可とともに割り当てることができます。

プレビュー ポータルでアクセス制御を開始するには、**[アクセス]** アイコンを選択します。

![ユーザーの選択](./media/resource-group-create-service-principal-portal/select-users.png)

アプリケーションを割り当てるロールを選択し、アプリケーションを選択します。

![ユーザーの選択](./media/resource-group-create-service-principal-portal/assign-to-role.png)

ポータルを使用してユーザーやアプリケーションをロールに割り当てる方法の詳細については、「[Azure 管理ポータルを使用したアクセス権の管理](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)」を参照してください。

## アクセス トークンをコードで取得する

.NET を使用している場合は、次のコードを使用し、アプリケーションのアクセス トークンを取得できます。

まず、Visual Studio プロジェクトに Active Directory 認証ライブラリをインストールする必要があります。これを行う最も簡単な方法は、NuGet パッケージを使用することです。パッケージ マネージャー コンソールを開き、次のコマンドを入力します。

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

アプリケーションで、次のようなメソッドを追加し、トークンを取得します。

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

## 次のステップ

- セキュリティ ポリシーの指定方法の詳細については、「[リソースへのアクセスの管理と監査](resource-group-rbac.md)」を参照してください。  
- これらの手順のビデオ デモについては、「[Enabling Programmatic Management of an Azure Resource with Azure Active Directory (Azure Active Directory で Azure リソースのプログラムによる管理を有効にする)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)」を参照してください。
- Azure PowerShell または Azure CLI を利用し、Active Directory のアプリケーションとサービス プリンシパルを使用する方法については、認証時の証明書の利用方法も含め、「[Azure リソース マネージャーでのサービス プリンシパルの認証](./resource-group-authenticate-service-principal.md)」を参照してください。
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

<!---HONumber=AcomDC_1203_2015-->