---
title: "Azure Active Directory B2C: ユーザー移行の方法"
description: "Graph API を使用した、および任意で Azure AD B2C のカスタム ポリシーを使用した、ユーザー移行の主要かつ高度なコンセプトについて説明します。"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/04/2017
ms.author: yoelh
ms.openlocfilehash: b102edd997e69fb3568780a42dfe93fc9a90e332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: ユーザー移行
ご利用の ID プロバイダーを Azure AD B2C に移行する場合は、ユーザー アカウントも移行する必要がある場合があります。 この記事では、既存のユーザー アカウントを ID プロバイダーから Azure AD B2C に移行する方法を説明します。 この記事の内容はこうしなければならないというものではなく、いくつかある方法のうちの 2 つを紹介しています。  どの方法が適切であるかを判断する責任は開発者にあります。

## <a name="user-migration-flows"></a>ユーザー移行のフロー
Azure AD B2C を使用すると、[Graph API](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) を通じてユーザーを移行できます。 ユーザー移行プロセスは、次の 2 つのフローに分類されます。

* **移行前** - このフローは、クリア テキストのユーザー資格情報 (ユーザー名とパスワード) にアクセスできる場合に適しています。 あるいは、資格情報が暗号化されていても復号化できる場合です。 このプロセスには、旧 ID プロバイダーからユーザーを読み込み、Azure AD B2C ディレクトリで新しいアカウントを作成する作業も含まれます。

* **移行前とパスワードのリセット** - このフローは、ユーザーのパスワードにアクセスできない場合に適しています。 For example:
    * パスワードがハッシュ形式で格納されている
    * パスワードが ID プロバイダーによって格納されており、アクセスできない。 利用していた旧 ID プロバイダーが、Web サービスを呼び出してユーザー資格情報を検証している

どちらのフローでも、最初に__移行前__プロセスを実行し、ご利用の旧 ID プロバイダーからユーザーを読み込み、Azure AD B2C ディレクトリで新しいアカウントを作成します。 パスワードがない場合は、ランダム パスワードを生成してアカウントを作成します。 ユーザーにパスワードを変更するように通知します。 または、ユーザーが初めてサインインするときに、ユーザーにパスワードをリセットするよう B2C から通知します。

## <a name="password-policy"></a>パスワード ポリシー
Azure AD B2C のパスワード ポリシー (ローカル アカウント用) は、Azure AD のポリシーに基づいています。 Azure AD B2C のサインアップまたはサインインとパスワード リセットの各ポリシーでは、"強力な" パスワード強度を使用しており、いずれのパスワードにも有効期限がありません。 詳細については、 [Azure AD のパスワード ポリシー](https://msdn.microsoft.com/library/azure/jj943764.aspx) に関するページを参照してください。

移行するアカウントのパスワード強度が [Azure AD B2C によって適用された強力なパスワード強度](https://msdn.microsoft.com/library/azure/jj943764.aspx)より低い場合は、強力なパスワードという要件を無効にすることができます。 既定のパスワード ポリシーを変更するには、`passwordPolicies` プロパティを `DisableStrongPassword` に設定します。 たとえば、ユーザー作成要求を次のように変更できます。 

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-using-graph-api-to-migrate-users"></a>手順 1: Graph API を使用してユーザーを移行する
Graph API を使用して Azure AD B2C ユーザー アカウントを (パスワードまたはランダム パスワードで) 作成します。 このセクションでは、Graph API を使用して Azure AD B2C ディレクトリでユーザー アカウントを作成する方法について説明します。

### <a name="step-11-register-your-application-in-your-tenant"></a>手順 1.1 テナントにアプリケーションを登録する
Graph API と通信するには、まず管理特権を持つサービス アカウントを持っている必要があります。 Azure AD 内で、アプリケーションと認証を Azure AD に登録します。 アプリケーションの資格情報は、**アプリケーション ID** と**アプリケーション シークレット**です。 アプリケーションはユーザーとしてではなくアプリケーションそれ自体として Graph API を呼び出します。

最初に、移行するアプリケーションを Azure AD に登録します。 次に、アプリケーション キー (アプリケーション シークレット) を作成し、適切な権限を使用してアプリケーションを設定します。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD **B2C** テナントを選択します。
3. 左側のパネルで、**[Azure Active Directory]** (Azure AD B2C ではありません) をクリックします。 これを見つけるために、**[その他のサービス]** を選択する必要がある場合があります。
4. **[アプリの登録]** を選択します。
5. **[新しいアプリケーションの登録]** をクリックします。

    ![[新しいアプリケーションの登録]](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
6. 画面の指示に従い、新しいアプリケーションを作成します
    * **名前**は、「**B2CUserMigratioin**」、または他の任意の名前にします。
    * **[アプリケーションの種類]** には **[Web アプリ/API]** を使用します。
    * **[サインオン URL]** は、「https://localhost」 (このアプリケーションでは関係しません) にします。
    * **Create** をクリックしてください。
7. アプリケーションを作成したら、アプリケーション一覧から新しく作成された **B2CUserMigratioin** アプリケーションを選択します。
**[プロパティ]** を選択し、**アプリケーション ID** をコピーして、後で使用するために保存します。

### <a name="step-12-create-application-secret"></a>手順 1.2 アプリケーション シークレットを作成する
8. Azure Portal の [登録済みのアプリ] で手順を続行します。 **[キー]** をクリックして、新しいキー (クライアント シークレットとも呼ばれます) を追加します。 また、後で使用するためにキーをコピーしておきます。

    ![[アプリケーション ID] と [キー]](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)

### <a name="step-13-grant-administrative-permission-to-your-application"></a>手順 1.3 アプリケーションに管理権限を付与する
9. Azure Portal の **[登録済みのアプリ]** で手順を続行します。
10. **[必要なアクセス許可]** をクリックします。
11. **[Microsoft Azure の Active Directory]** をクリックします。
12. **[アクセスの有効化]** で、**[アプリケーションのアクセス許可]** の **[ディレクトリ データの読み取りと書き込み]** 許可を選択し、**[保存]** をクリックします。
13. 最後に、**[必要なアクセス許可]** に戻り、**[アクセス許可の付与]** をクリックします。

    ![アプリケーションのアクセス許可](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)

これで B2C テナントに対してユーザーの作成、読み取り、更新を実行する権限をアプリケーションに付与できました。

### <a name="step-14-optional-environment-cleanup"></a>手順 1.4 (省略可能) 環境のクリーンアップ
[ディレクトリ データの読み取りと書き込み] 許可には、ユーザーを削除する権限は含まれていません。 ご利用のアプリケーションでユーザーを削除できるようにする (ご利用の環境をクリーンアップする) には、追加の手順が必要になります。 この手順には PowerShell を実行して __[ユーザー アカウント管理者]__ 権限を設定する作業が含まれますが、必要ない場合は次のセクションをスキップしてください。


> [!IMPORTANT]
> B2C テナントに**固有**の B2C テナントの管理者アカウントを使用する必要があります。 そのアカウントは次のようになります: admin@contosob2c.onmicrosoft.com

>[!NOTE]
> 以下の PowerShell スクリプトを実行するには、[Azure Active Directory PowerShell **バージョン 2**](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) が必要になります。

以下の PowerShell スクリプトでは
* ご利用のオンライン サービスに接続します。 そのためには、Windows PowerShell コマンド プロンプトで `Connect-AzureAD` コマンドレットを実行して、資格情報を入力します。 
* **アプリケーション ID** を使用して、アプリケーションにユーザー アカウント管理者ロールを割り当てます。 このロールにはよく使用される識別子が付いているため、必要な作業はスクリプトに**アプリケーション ID** を入力することだけです。

```PowerShell
Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

`$AppId` の値を Azure AD の **アプリケーション ID** に変更します。

## <a name="step-2-pre-migration-application-sample"></a>手順 2: 移行前のアプリケーション サンプル
サンプル コードをダウンロードして実行します。 [サンプル コードは .zip ファイルでダウンロード](http://www.github.com)できます。

### <a name="step-21-edit-the-migration-data-file"></a>手順 2.1 移行データ ファイルを編集する
このサンプル アプリでは、ダミーのユーザー データを含む JSON ファイルを使用します。 サンプルを正常に実行できたら、ご利用のデータベースからデータを読み込むようにコードを変更します。 または、ユーザー プロファイルを JSON ファイルにエクスポートして、そのファイルを使用するようにアプリを設定します。
JSON ファイルを編集するには、`AADB2C.UserMigration.sln` Visual Studio ソリューションを開きます。 `AADB2C.UserMigration` プロジェクトで、`UsersData.json` ファイルを開きます。


![ユーザー データ ファイル](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

ご覧のように、このファイルにはユーザー エンティティの一覧が含まれています。 各ユーザー エンティティは次の情報を持っています。
* email
* displayName
* firstName
* lastName
* パスワード (空にできます)

> [!NOTE]
> コンパイル時に Visual Studio はファイルを `bin` ディレクトリにコピーします。

### <a name="step-22-configure-the-application-settings"></a>手順 2.2 アプリケーション設定を構成する
`AADB2C.UserMigration` プロジェクトで、App.config ファイルを開きます。 以下のアプリ設定を独自の値に置き換えます。

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users data e.g. UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure Table string}" />
    
</appSettings>
```

> [!NOTE]
> * Azure Table 接続文字列の使用については、後半のセクションで説明します
> * B2C テナントの名前はテナントの作成時に入力したドメインであり、Azure Portal に表示されます。 通常、`.onmicrosoft.com` という接尾辞が付けられます (`contosob2c.onmicrosoft.com` など)。
>

### <a name="step-23-run-the-pre-migration-process"></a>手順 2.3 移行前プロセスを実行する
`AADB2C.UserMigration` ソリューションを右クリックして、サンプルをリビルドします。 リビルドできると、`AADB2C.UserMigration\bin\Debug` に `UserMigration.exe` 実行可能ファイルが表示されます。 移行プロセスを実行するには、次のコマンド ライン パラメーターのいずれかを使用します。

* **パスワードを使用してユーザーを移行する**には、`UserMigration.exe 1` コマンドを使用します。

* **ランダム パスワードを使用してユーザーを移行する**には、`UserMigration.exe 2` コマンドを使用します。 この操作により、Azure Table エンティティも作成されます。 後でポリシーを構成して REST API サービスを呼び出すようにします。 このサービスは Azure Table を使用して、移行プロセスを追跡し管理します。

![移行プロセスのデモ](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>手順 2.4 移行前プロセスを確認する
結果を確認するには、Azure Portal で **Azure AD B2C** を開き、**[ユーザーとグループ]** をクリックします。 検索ボックスにユーザーの表示名の 1 つを入力して、ユーザー プロファイルを表示します。 または、このサンプル アプリケーションで**サインイン メール アドレス**を使用してユーザーを取得することもできます。 サインイン メール アドレスでユーザーを検索するには、次のコマンドを実行します

```Console
UserMigration.exe 3 {email address}
```

次のように、出力を JSON ファイルに保存することもできます。
```Console
UserMigration.exe 3 {email address} >> UserProfile.json
```


好きな JSON エディターを使用して、UserProfile.json ファイルを開きます。 Visual Studio Code で `Shift+Alt+F` キー、またはコンテキスト メニューの [ドキュメントのフォーマット] を使用して、JSON ドキュメントをフォーマットできます。

![ユーザー プロファイル JSON](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

`UserMigration.exe 4 <Display name>` コマンドで**表示名**を使用して、ユーザーを取得することもできます

### <a name="step-25-optional-environment-cleanup"></a>手順 2.5 (省略可能) 環境のクリーンアップ
Azure AD テナントをクリーンアップして Azure AD ディレクトリからユーザーを削除する場合は、`UserMigration.exe 5` コマンドを実行します

> [!NOTE]
> * テナントをクリーンアップするには、ご利用のアプリケーションに __[ユーザー アカウント管理者]__ 権限を付与します
> * サンプルの移行アプリでは、JSON ファイルに記述されているすべてのユーザーをクリーンアップします

### <a name="step-26-sing-in-with-migrated-users-with-password"></a>手順 2.6 移行したユーザーでサインインする (パスワードを使用)
ユーザーのパスワードを使用して移行前プロセスを実行すると、アカウントを使用する準備が完了し、Azure AD B2C を使用してご利用のアプリケーションにユーザーがサインインできるようになります。 ユーザーのパスワードにアクセスできない場合は、次のセクションをご覧ください。

## <a name="step-3-password-reset"></a>手順 3: パスワードをリセットする
ランダム パスワードを使用してユーザーを移行する場合は、ユーザー自身がパスワードをリセットする必要があります。 パスワードをリセットするには
* ウェルカム メールにパスワードをリセットするためのリンクを記載して送信します
* (省略可能) ユーザーがパスワードをリセットせずにサインインしようとしても対処できるよう、ご利用のポリシーを変更します。 サインイン時にポリシーによって移行の状態を確認します。 ユーザーがパスワードを変更しなかった場合は、親切なエラー メッセージを表示して [パスワードを忘れた場合] をクリックするように促します。

    > [!NOTE]
    > ユーザー移行の状態を確認して変更するには、カスタム ポリシーを使用する必要があります。 [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。
    >

### <a name="step-31-send-a-welcome-email-with-link-to-reset-password"></a>手順 3.1 ウェルカム メールにパスワードをリセットするためのリンクを記載して送信する
パスワード リセット ポリシーへのリンクを取得するには

1.  **Azure AD B2C の設定**を開いたあと、**[パスワードのリセット]** ポリシーのプロパティを開きます。

2. アプリケーションを選択します。
    >[!NOTE]
    >
    >**[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。  

2.  **[今すぐ実行]** を選択して、ポリシーを確認します
3.  URL を**コピー**して、その URL をユーザーに送信します

    ![診断ログの設定](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-user-migration-status"></a>手順 4: (省略可能) ユーザー移行の状態を確認して設定するようポリシーを変更する

ユーザーが最初にパスワードをリセットせずにサインインしようとした場合に、分かりやすいエラー メッセージをポリシーが返すようにする必要があります。 例: パスワードが期限切れです。パスワードをリセットするには、リセット パスワード リンクをクリックしてください。  この任意の手順では、[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事で説明したように、カスタム ポリシーを使用して Azure AD B2C を使用する必要があります。

このセクションでは、サインイン時に移行の状態を確認するようポリシーを変更します。 ユーザーがパスワードを変更しなかった場合は、HTTP 409 のエラー メッセージを返して [パスワードを忘れた場合]  のリンクをクリックするように促します。 パスワードの変更を追跡するには、Azure Table を使用します。 コマンド ライン パラメーターの `2` を使用して移行前プロセスを実行し、Azure Table にユーザー エンティティを作成します。 ご利用のサービス:

* サインイン時に、Azure AD B2C ポリシーは移行の RESTful サービスを呼び出して、入力要求のメールを送信します。 このサービスは Azure Table でメール アドレスを検索します。 該当するものが存在する場合は、"パスワードを変更する必要がある" という内容のエラー メッセージを表示します。

* パスワードのリセット時には、ユーザーがパスワードの変更に成功したあと、Azure Table からそのエンティティを削除します。


> [!NOTE]
>サンプルを単純にするために、ここでは Azure Table を使用します。 移行の状態は任意のデータベースに格納できます。または、Azure AD B2C アカウントにカスタム プロパティとして格納できます。

### <a name="41-application-settings"></a>4.1 アプリケーションの設定
デモの Restful API をテストします。 `AADB2C.UserMigration.sln` Visual Studio ソリューションを Visual Studio で開きます。 `AADB2C.UserMigration.API` プロジェクトで、App.config ファイルを開きます。アプリの 3 つの設定を独自の値に置き換えます。

```XML
<appSettings>
    <add key="BlobStorageConnectionString" value="{The Azure Blob storage connection string"} />
</appSettings>
```

### <a name="step-42-deploy-your-web-application-to-azure-app-services"></a>手順 4.2 Azure App Service に Web アプリケーションをデプロイする
Azure App Service にご利用の API サービスを発行します。 詳細については、[Azure App Service へのアプリのデプロイ](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)に関する記事をご覧ください。

### <a name="step-43-add-technical-profile-and-technical-profile-validation-to-your-policy"></a>手順 4.3 ポリシーに技術プロファイルと技術プロファイル検証を追加する 
1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。 
2. `<ClaimsProviders>` セクションを探します。
3. `ClaimsProviders` 要素の下に次の XML スニペットを追加します。
4. ご利用のエンドポイント URL を指すよう、`ServiceUrl` の値を変更します。 

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>

    <TechnicalProfile Id="LocalAccountSignIn">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountPasswordReset">
        <DisplayName>Local account just in time migration</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        </InputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>

<ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>

    <!-- This technical profile uses a validation technical profile to authenticate the user. -->
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountSignIn" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    <TechnicalProfile Id="LocalAccountWritePasswordUsingObjectId">
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="LocalAccountPasswordReset" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

技術プロファイルは 1 つの入力要求 `signInName` を定義します (メールとして送信)。 サインイン時に、この要求がご利用の RESTful エンドポイントに送信されます。

ご利用の RESTful API 用の技術プロファイルを定義したあと、Azure AD B2C ポリシーがその技術プロファイルを呼び出すようにします。 XML スニペットは、基本ポリシーで定義されている `SelfAsserted-LocalAccountSignin-Email` を上書きします。 また、XML スニペットは、技術プロファイル `LocalAccountUserMigration` を指す ReferenceId を使用して、`ValidationTechnicalProfile` を追加します。 

### <a name="step-44-upload-the-policy-to-your-tenant"></a>手順 4.4 ポリシーをテナントにアップロードする
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替え、**[Azure AD B2C]** をクリックします。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** をクリックします。
4.  **[Upload Policy]**(ポリシーのアップロード) を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。
7.  最後の手順を繰り返して、SignUpOrSignIn.xml をアップロードします。

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>手順 4.5 [今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。
2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3. 移行したユーザーのいずれかでサインインを試み、**[サインイン]** をクリックします。 REST API からの次のエラー メッセージが表示されるはずです。

    ![診断ログの設定](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshooting-your-rest-api"></a>手順 4.6 (省略可能) REST API のトラブルシューティング
ほぼリアルタイムでログ情報を監視して確認できます。

1. RESTful アプリケーションの設定メニューで、**[監視]** セクションまで下にスクロールし、**[診断ログ]** をクリックします。 
2. アプリケーション ログを有効にします (ファイル システム) 
3. **[レベル]** を **[詳細]** レベルに設定します。
4. **[保存]**

    ![診断ログの設定](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

5. 設定メニューで、**[ログ ストリーム]** をクリックします。
6. RESTful API の出力を確認します。

詳細については、[ストリーミング ログとコンソール](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-streaming-logs-and-console)に関する記事をご覧ください。

> [!IMPORTANT]
> __診断ログ__は、開発とテストの期間中のみ使用します。 RESTful API の出力には、運用環境で公開すべきではない機密情報が含まれている場合があります。
>

## <a name="download-the-complete-policy-files"></a>完全なポリシー ファイルをダウンロードする
省略可能: これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)