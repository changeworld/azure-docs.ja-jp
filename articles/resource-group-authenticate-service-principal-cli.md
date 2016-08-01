<properties
   pageTitle="Azure CLI を使用したサービス プリンシパルの作成 | Microsoft Azure"
   description="Azure CLI を使用して、Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [ポータル](resource-group-create-service-principal-portal.md)

リソースへのアクセスを必要とするアプリケーションやスクリプトがあるとき、そのプロセスを特定のユーザーの資格情報で実行すると高い確率で不都合が生じます。ユーザーの権限がプロセスに割り当てるべき権限と異なっていたり、ユーザーの職責が変わったりする可能性があります。そのような場合は、認証の資格情報とロールの割り当てを含んだアプリケーションの ID を作成します。アプリケーションは、その実行時に都度、この ID でログインすることになります。このトピックでは、アプリケーションをその独自の資格情報と ID で実行させるために必要な設定をすべて [Mac、Linux、Windows 用の Azure CLI](xplat-cli-install.md) で行う方法を紹介しています。

この記事では、Active Directory (AD) アプリケーションとサービス プリンシパルの 2 つのオブジェクトを作成します。この AD アプリケーションは、資格情報としてアプリケーション ID とパスワード (またはアプリケーション ID と証明書) を保持します。サービス プリンシパルは、ロールの割り当てを保持します。AD アプリケーションから、多くのサービス プリンシパルを作成できます。このトピックでは、シングル テナント アプリケーション (単一組織内で実行することのみが意図されたアプリケーション) に焦点を絞って説明します。一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。アプリケーションを複数の組織で実行する必要があるときは、マルチテナント アプリケーションを作成することもできます。一般に、Software-as-a-Service (SaaS) アプリケーションには、マルチテナント アプリケーションが使用されます。マルチテナント アプリケーションのセットアップについては、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

Active Directory との連携では、理解すべき概念が数多く存在します。アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](./active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](./active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

AD アプリケーションの認証に関して、Azure CLI には次の 2 つの選択肢があります。

 - パスワード
 - 証明書

AD アプリケーションのセットアップ後、別のプログラミング フレームワーク (Python、Ruby、Node.js など) から Azure にログインする予定がある場合、パスワード認証が最適な選択肢と考えられます。パスワードと証明書のどちらを使用するか決める前に、「[サンプル アプリケーション](#sample-applications)」セクションで、各種フレームワークにおける認証の例を参照してください。

## テナント ID を取得する

サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。テナントは、Active Directory のインスタンスです。テナント ID の値は、パスワード認証と証明書認証のどちらであっても必要となるので、ここで取得しておきましょう。

1. ご使用のアカウントにサインインします。

        azure config mode arm
        azure login

1. 現在認証されているサブスクリプションのテナント ID を取得する場合は、サブスクリプション ID をパラメーターとして渡す必要はありません。**-r** スイッチは、二重引用符なしで値を取得します。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

ここまで終えたら、[パスワード](#create-service-principal-with-password)認証または[証明書](#create-service-principal-with-certificate)認証のセクションに進みます。


## パスワードを使用したサービス プリンシパルの作成

このセクションでは、パスワードを使用してサービス プリンシパルを作成し、それをロールに割り当てる手順を実行します。

1. アプリケーションのサービス プリンシパルを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。このコマンドによって、AD アプリケーションとサービス プリンシパルの両方が作成されます。

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。ログイン時には、サービス プリンシパル名が必要となります。
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。ロールを割り当てるには、[所有者](./active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与される `Microsoft.Authorization/*/Write` アクセス権が必要です。

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。次のセクションでは、Azure CLI から資格情報を使ってログインする方法を紹介していますが、独自コードのアプリケーションから資格情報を使用する場合は、特に読む必要はありません。「[サンプル アプリケーション](#sample-applications)」に進んで、アプリケーション ID とパスワードでログインする例をご覧ください。

### 資格情報を Azure CLI で渡す

1. ユーザー名には、サービス プリンシパルを作成するときに返されたサービス プリンシパル名を使用します。アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。**search** パラメーターに、Active Directory アプリケーションの名前を指定します。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. サービス プリンシパルとしてログインします。

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant "$tenantId"

    パスワードを入力するように求められます。AD アプリケーションの作成時に指定したパスワードを使用します。

        info:    Executing command login
        Password: ********

これで、作成したサービス プリンシパルのサービス プリンシパルとして認証されました。

## 証明書を使用したサービス プリンシパルの作成

ここでは、認証に証明書を使用するサービス プリンシパル用のサービス プリンシパルを作成します。これらの手順を完了するには、[OpenSSL](http://www.openssl.org/) がインストールされている必要があります。

1. 自己署名証明書を作成します。

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. パブリックとプライベート キーを組み合わせます。

        cat privkey.pem cert.pem > examplecert.pem

3. **examplecert.pem** ファイルを開き、証明書データをコピーします。**-----BEGIN CERTIFICATE-----** と **-----END CERTIFICATE-----** の間の長い文字のシーケンスを探します。

1. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。ロールを割り当てるには、[所有者](./active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与される `Microsoft.Authorization/*/Write` アクセス権が必要です。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 自動化された Azure CLI スクリプトから証明書を渡す

Azure CLI で認証するには、証明書の拇印、証明書のファイル、アプリケーション ID、およびテナント ID を渡します。

    azure login --service-principal --tenant 00000 -u 000000 --certificate-file C:\certificates\examplecert.pem --thumbprint 000000

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

証明書の拇印を取得し、不要な文字を削除する必要がある場合は、次のコマンドを使用します。

    openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
次のような拇印の値が返されます。

    30996D9CE48A0B6E0CD49DBB9A48059BF9355851

アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。

    azure ad app show --search exampleapp --json | jq -r '.[0].appId'

テナント ID を後から取得する必要がある場合は、「[テナント ID を取得する](#get-tenant-id)」を参照してください。


## サンプル アプリケーション

サービス プリンシパルとしてログインする方法については、以下のサンプル アプリケーションで紹介されています。

**.NET**

- [.NET からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Azure のリソースとリソース グループを .NET で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [リソースの概要 - Java で Azure Resource Manager テンプレートをデプロイする](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [リソースの概要 - Java でリソース グループを管理する](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Python からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Azure のリソースとリソース グループを Python で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Node.js からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Azure のリソースとリソース グループを Node.js で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Ruby からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Azure のリソースとリソース グループを Ruby で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## 次のステップ
  
- アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
- 証明書と Azure CLI の使用方法の詳細については、「[Linux コマンドラインからの Azure サービス プリンシパルの証明書での認証](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->