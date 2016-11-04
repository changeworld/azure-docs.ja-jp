---
title: Azure CLI を使用したサービス プリンシパルの作成 | Microsoft Docs
description: Azure CLI を使用して、Active Directory アプリケーションやサービス プリンシパルを作成し、ロールベースのアクセス制御によって、リソースへのアクセス権を付与する方法について説明します。パスワードまたは証明書を使ってアプリケーションを認証する方法を示します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/07/2016
ms.author: tomfitz

---
# リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Azure CLI](resource-group-authenticate-service-principal-cli.md)
> * [ポータル](resource-group-create-service-principal-portal.md)
> 
> 

リソースへのアクセスを必要とするアプリケーションやスクリプトがある場合、そのプロセスをお客様自身の資格情報で実行すると高い確率で不都合が生じます。アプリケーション用に別のアクセス許可が必要になるほか、担当が変わった場合もアプリケーションではお客様の資格情報が引き続き使用されることになります。このような場合は、認証の資格情報とロールの割り当てを含んだアプリケーションの ID を作成します。アプリケーションは、実行のたびにこれらの資格情報を使用して自動認証を行います。このトピックでは、アプリケーションをその独自の資格情報と ID で実行させるために必要な設定を [Mac、Linux、Windows 用の Azure CLI](xplat-cli-install.md) で行う方法を紹介しています。

AD アプリケーションの認証に関して、Azure CLI には次の 2 つの選択肢があります。

* パスワード
* 証明書

このトピックでは、Azure CLI でのこれらの選択肢の使用方法について説明します。プログラミング フレームワーク (Python、Ruby、Node.js など) から Azure にログインする場合は、パスワード認証が最適な選択肢と考えられます。パスワードと証明書のどちらを使用するか決める前に、「[サンプル アプリケーション](#sample-applications)」セクションで、各種フレームワークにおける認証の例を参照してください。

## Active Directory の概念
この記事では、Active Directory (AD) アプリケーションとサービス プリンシパルという 2 つのオブジェクトを作成します。AD アプリケーションは、アプリケーションをグローバルに表現したものです。資格情報としてアプリケーション ID と、パスワードまたは証明書のいずれかを保持します。サービス プリンシパルは、Active Directory のアプリケーションをローカルに表現したものです。こちらは、ロールの割り当てを保持します。このトピックでは、シングル テナント アプリケーション (単一組織内でのみ実行するように意図されたアプリケーション) に焦点を絞って説明します。一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。シングルテナント アプリケーションでは、1 つの AD アプリケーションと 1 つのサービス プリンシパルを設定します。

両方のオブジェクトが必要になるわけを疑問に思う方もいるでしょう。 このような方法の利点は、マルチテナント アプリケーションについて考えるとはっきりします。通常、マルチテナント アプリケーションは、さまざまなサブスクリプションでアプリケーションが実行されるサービスとしてのソフトウェア (SaaS) アプリケーションで使用します。マルチテナント アプリケーションでは、1 つの AD アプリケーションと複数のサービス プリンシパル (1 つは Active Directory でアプリケーションにアクセスを付与するためのもの) を設定できます。マルチテナント アプリケーションのセットアップについては、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

## 必要なアクセス許可
このトピックを完了するには、Azure Active Directory と Azure サブスクリプションの両方で適切なアクセス許可を持っている必要があります。具体的には、Active Directory でアプリケーションを作成し、ロールにサービス プリンシパルを割り当てることができる必要があります。

Active Directory のアカウントは管理者 (**全体管理者**や**ユーザー管理者**など) である必要があります。アカウントが**ユーザー** ロールに割り当てられている場合は、管理者に依頼してアクセス許可のレベルを上げてもらう必要があります。

サブスクリプションのアカウントには `Microsoft.Authorization/*/Write` アクセス権が必要です。このアクセス権は、[所有者](active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与されます。アカウントが**共同作成者**ロールに割り当てられている場合、ロールにサービス プリンシパルを割り当てようとするとエラーが発生します。ここでも、サブスクリプション管理者に適切なアクセス権を付与してもらう必要があります。

ここで、[パスワード](#create-service-principal-with-password)認証または[証明書](#create-service-principal-with-certificate)認証に関するセクションに進みます。

## パスワードを使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* パスワードを使用した AD アプリケーションと、サービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順をすぐに実行するには、次のコマンドを使用します。

    azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
    azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

それでは、各プロセスについて理解できるように、これらの手順についてより細かく見ていきましょう。

1. ご使用のアカウントにサインインします。
   
        azure config mode arm
        azure login
2. アプリケーションのサービス プリンシパルを作成します。表示名、アプリケーションを説明するページへの URI、アプリケーションを識別する URI、およびアプリケーション ID のパスワードを指定します。このコマンドによって、AD アプリケーションとサービス プリンシパルの両方が作成されます。
   
        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {your-password}
   
     シングルテナント アプリケーションでは、URI の検証は行われません。
   
     Active Directory のアカウントに[必要なアクセス許可](#required-permissions)が設定されていない場合、"認証が承認されなかったこと" または "コンテキストにサブスクリプションが見つからなかったこと" を示すエラー メッセージが表示されます。
   
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
3. サブスクリプションに対する権限をサービス プリンシパルに付与します。この例では、**閲覧者**ロールにサービス プリンシパルを追加します。これにより、サブスクリプション内のすべてのリソースを読み取るアクセス許可が付与されます。その他のロールについては、「[RBAC: 組み込みのロール](active-directory/role-based-access-built-in-roles.md)」を参照してください。**ServicePrincipalName** パラメーターには、アプリケーションの作成時に使用した **ObjectId** を指定します。
   
        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   
     ロールを割り当てることのできるアクセス許可がアカウントに設定されていない場合は、エラー メッセージが表示されます。このメッセージは、アカウントが**スコープ '/subscriptions/{GUID}' に対するアクション 'Microsoft.Authorization/roleAssignments/write' の実行を承認されていない**ことを示しています。

これで完了です。 AD アプリケーションとサービス プリンシパルが設定されました。次のセクションでは、Azure CLI から資格情報を使ってログインする方法を紹介します。コード アプリケーションの資格情報を使用する場合は、このトピックを続行する必要はありません。「[サンプル アプリケーション](#sample-applications)」に進んで、アプリケーション ID とパスワードでログインする例をご覧ください。

### 資格情報を Azure CLI で渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。

1. サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。テナントは、Active Directory のインスタンスです。現在認証されているサブスクリプションのテナント ID を取得するには、次のコマンドを使用します。
   
        azure account show
   
     次のような結果が返されます。
   
        info:    Executing command account show
        data:    Name                        : Microsoft Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...
   
     別のサブスクリプションのテナント ID を取得する必要がある場合は、次のコマンドを使用します。
   
        azure account show -s {subscription-id}
2. サービス プリンシパルとしてログインします。
   
        azure login -u https://www.contoso.org/example --service-principal --tenant {tenant-id}
   
    パスワードを入力するように求められます。AD アプリケーションの作成時に指定したパスワードを使用します。
   
        info:    Executing command login
        Password: ********

これで、作成したサービス プリンシパルのサービス プリンシパルとして認証されました。

## 証明書を使用したサービス プリンシパルの作成
このセクションでは次の手順を実行します。

* 自己署名証明書を作成する
* 証明書を使用する AD アプリケーションとサービス プリンシパルを作成する
* サービス プリンシパルに閲覧者ロールを割り当てる

これらの手順を完了するには、[OpenSSL](http://www.openssl.org/) がインストールされている必要があります。

1. 自己署名証明書を作成します。
   
        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
2. パブリックとプライベート キーを組み合わせます。
   
        cat privkey.pem cert.pem > examplecert.pem
3. **examplecert.pem** ファイルを開き、**-----BEGIN CERTIFICATE-----** と **-----END CERTIFICATE-----** の間の長い文字のシーケンスを探します。証明書データをコピーします。サービス プリンシパルを作成するときに、このデータをパラメーターとして渡します。
4. ご使用のアカウントにサインインします。
   
        azure config mode arm
        azure login
5. アプリケーションのサービス プリンシパルを作成します。表示名、アプリケーションを説明するページへの URI、アプリケーションを識別する URI、およびコピーした証明書データを指定します。このコマンドによって、AD アプリケーションとサービス プリンシパルの両方が作成されます。
   
        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
   
     シングルテナント アプリケーションでは、URI の検証は行われません。
   
     Active Directory のアカウントに[必要なアクセス許可](#required-permissions)が設定されていない場合、"認証が承認されなかったこと" または "コンテキストにサブスクリプションが見つからなかったこと" を示すエラー メッセージが表示されます。
   
     新しいサービス プリンシパルが返されます。アクセス許可を付与する場合は、オブジェクト ID が必要です。
   
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
6. サブスクリプションに対する権限をサービス プリンシパルに付与します。この例では、**閲覧者**ロールにサービス プリンシパルを追加します。これにより、サブスクリプション内のすべてのリソースを読み取るアクセス許可が付与されます。その他のロールについては、「[RBAC: 組み込みのロール](active-directory/role-based-access-built-in-roles.md)」を参照してください。**ServicePrincipalName** パラメーターには、アプリケーションの作成時に使用した **ObjectId** を指定します。
   
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   
     ロールを割り当てることのできるアクセス許可がアカウントに設定されていない場合は、エラー メッセージが表示されます。このメッセージは、アカウントが**スコープ '/subscriptions/{GUID}' に対するアクション 'Microsoft.Authorization/roleAssignments/write' の実行を承認されていない**ことを示しています。

### 自動化された Azure CLI スクリプトから証明書を渡す
次に、操作を実行するアプリケーションとしてログインする必要があります。

1. サービス プリンシパルとしてサインインするときは常に、AD アプリのディレクトリのテナント ID を指定する必要があります。テナントは、Active Directory のインスタンスです。現在認証されているサブスクリプションのテナント ID を取得するには、次のコマンドを使用します。
   
        azure account show
   
     次のような結果が返されます。
   
        info:    Executing command account show
        data:    Name                        : Microsoft Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...
   
     別のサブスクリプションのテナント ID を取得する必要がある場合は、次のコマンドを使用します。
   
        azure account show -s {subscription-id}
2. 証明書の拇印を取得し、不要な文字を削除するには、次のコマンドを使用します。
   
        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   
     次のような拇印の値が返されます。
   
        30996D9CE48A0B6E0CD49DBB9A48059BF9355851
3. サービス プリンシパルとしてログインします。
   
        azure login --service-principal --tenant {tenant-id} -u https://www.contoso.org/example --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

これで、作成した Active Directory アプリケーションのサービス プリンシパルとして認証されました。

## サンプル アプリケーション
サービス プリンシパルとしてログインする方法については、以下のサンプル アプリケーションで紹介されています。

**.NET**

* [.NET からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Azure のリソースとリソース グループを .NET で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [リソースの概要 - Java で Azure Resource Manager テンプレートをデプロイする](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [リソースの概要 - Java でリソース グループを管理する](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Python からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Azure のリソースとリソース グループを Python で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Node.js からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Azure のリソースとリソース グループを Node.js で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Ruby からテンプレートを使用して SSH 対応 VM をデプロイする](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Azure のリソースとリソース グループを Ruby で管理する](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## 次のステップ
* アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。
* 証明書と Azure CLI の使用方法の詳細については、「[Certificate-based authentication with Azure Service Principals from Linux command line (Linux コマンド ラインからの Azure サービス プリンシパルの証明書での認証)](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->