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
   ms.date="05/26/2016"
   ms.author="tomfitz"/>

# リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [ポータル](resource-group-create-service-principal-portal.md)

このトピックでは、[Mac、Linux、Windows 用の Azure CLI](xplat-cli-install.md) を使用して、自動化プロセス、アプリケーション、またはサービスなどの、サブスクリプション内の他のリソースにアクセスできる Active Directory (AD) アプリケーションを作成する方法を示します。Azure Resource Manager では、ロール ベースのアクセス制御を使用して、アプリケーションへの許可されたアクションを付与することができます。

この記事では、AD アプリケーションとサービス プリンシパルの 2 つのオブジェクトを作成します。AD アプリケーションは、アプリが登録されているテナントに存在し、実行するプロセスを定義します。サービス プリンシパルには AD アプリケーションの ID が含まれており、この ID は権限を割り当てるために使用されます。AD アプリケーションから、多くのサービス プリンシパルを作成できます。アプリケーションとサービス プリンシパルの詳細については、「[アプリケーションおよびサービス プリンシパル オブジェクト](./active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](./active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

アプリケーションを認証する方法には 2 つのオプションがあります。

 - パスワード - 実行中に対話形式でログインする場合に適しています。
 - 証明書 - ユーザーの介入なしでの認証が必要な自動スクリプトに適しています。
 
## パスワードを使用した AD アプリケーションの作成

このセクションでは、パスワードを使用して AD アプリケーションを作成する手順を実行します。

1. Azure リソース マネージャー モードに切り替えて、アカウントにサインインします。

        azure config mode arm
        azure login

2. **azure ad app create** コマンドを実行して、新しい AA アプリケーションを作成します。アプリケーションの表示名、アプリケーションを説明するページへの URI (リンクが確認されていません)、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    AD アプリケーションが返されます。サービス プリンシパルの作成およびアクセス トークンの取得には、AppId プロパティが必要です。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### サービス プリンシパルの作成とロールへの割り当て

1. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

2. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### 資格情報を Azure CLI で手動で渡す

AD アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。操作を実行するアプリケーションとしてログインする必要があります。必要に応じて使用するスクリプトまたはコマンドを実行して、手動でアプリケーションの資格情報を渡すことができます。

1. サービス プリンシパルを含むサブスクリプションの **TenantId** を確定します。現在認証されているサブスクリプションのテナント ID を取得する場合は、サブスクリプション ID をパラメーターとして渡す必要はありません。**-r** スイッチは、二重引用符なしで値を取得します。

        tenantId=$(azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId')

2. ユーザー名には、サービス プリンシパルの作成時に使用した **AppId** を使用します。アプリケーション ID を取得する必要がある場合は、次のコマンドを使用します。**search** パラメーターに、Active Directory アプリケーションの名前を指定します。

        appId=$(azure ad app show --search exampleapp --json | jq -r '.[0].appId')

3. サービス プリンシパルとしてログインします。

        azure login -u "$appId" --service-principal --tenant "$tenantId"

    パスワードを入力するように求められます。AD アプリケーションの作成時に指定したパスワードを使用します。

        info:    Executing command login
        Password: ********

これで、作成した AD アプリケーションのサービス プリンシパルとして認証されました。

## 証明書を使用した AD アプリケーションの作成

ここでは、認証に証明書を使用する AD アプリケーション用のサービス プリンシパルを作成します。これらの手順を完了するには、[OpenSSL](http://www.openssl.org/) がインストールされている必要があります。

1. 自己署名証明書を作成します。

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. パブリックとプライベート キーを組み合わせます。

        cat privkey.pem cert.pem > examplecert.pem

3. **examplecert.pem** ファイルを開き、証明書データをコピーします。**-----BEGIN CERTIFICATE-----** と **-----END CERTIFICATE-----** 間の長い文字のシーケンスを探します。

4. **azure ad app create** コマンドを実行して新しい Active Directory アプリケーションを作成し、前の手順でコピーした証明書データをキーの値として提供します。

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Active Directory アプリケーションが返されます。サービス プリンシパルの作成およびアクセス トークンの取得には、AppId プロパティが必要です。

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

### サービス プリンシパルの作成とロールへの割り当て

1. アプリケーションのサービス プリンシパルを作成します。前の手順で返されたアプリケーション ID を入力します。

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. サブスクリプションに対する権限をサービス プリンシパルに付与します。このサンプルでは、サブスクリプション内のすべてのリソースを読み取る権限をサービス プリンシパルに付与します。ロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

### スクリプトの値を準備します。

スクリプトでは、サービス プリンシパルとしてログインするために必要な 3 つの値で渡します。次が必要です。

- アプリケーション ID
- テナント ID
- 証明書の拇印

前の手順でアプリケーション ID と証明書の拇印について説明しました。ただし、後でこれらの値を取得する必要がある場合は、そのコマンドを、テナント ID を取得するコマンドとともに以下に示します。

1. 証明書の拇印を取得し、不要な文字を削除するには、次のコマンドを使用します。

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     次のような拇印の値が返されます。

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. テナント ID を取得するには、次のコマンドを使用します。

        azure account show -s <subscriptionId> --json | jq -r '.[0].tenantId'

3. アプリケーション ID を取得するには、次のコマンドを使用します。

        azure ad app show --search exampleapp --json | jq -r '.[0].appId'

### 自動化された Azure CLI スクリプトから証明書を渡す

Active Directory アプリケーションとそのアプリケーション用のサービス プリンシパルの作成が完了しました。サービス プリンシパルをロールに割り当てました。これで、サービス プリンシパルとして操作できるよう、サービス プリンシパルとしてログインする必要があります。

Azure CLI で認証するには、証明書の拇印、証明書のファイル、アプリケーション ID、およびテナント ID を渡します。

        azure login --service-principal --tenant {tenant-id} -u {app-id} --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

## 次のステップ
  
- .NET 認証の例については、「[Azure Resource Manager SDK for .NET](resource-manager-net-sdk.md)」を参照してください。
- Java 認証の例については、「[Azure Resource Manager SDK for Java](resource-manager-java-sdk.md)」を参照してください。
- Python 認証の例については、「[Resource Management Authentication for Python (Python 向けリソース管理認証)](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)」を参照してください。
- REST 認証の例については、「[Resource Manager REST API](resource-manager-rest-api.md)」を参照してください。
- アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
- 証明書と Azure CLI の使用方法の詳細については、「[Linux コマンドラインからの Azure サービス プリンシパルの証明書での認証](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)」を参照してください。

<!---HONumber=AcomDC_0629_2016-->