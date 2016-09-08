<properties
   pageTitle="ポータルでサービス プリンシパルを作成する | Microsoft Azure"
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
   ms.date="08/18/2016"
   ms.author="tomfitz"/>

# リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルをポータルで作成する

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [ポータル](resource-group-create-service-principal-portal.md)


アプリケーションでリソースにアクセスしたり変更を加えたりするには、Active Directory (AD) アプリケーションをセットアップして、そこに必要な権限を割り当てる必要があります。このトピックでは、それらの手順をポータルで行う方法について説明します。現時点では、クラシック ポータルを使用して新しい Active Directory アプリケーションを作成したうえで、Azure ポータルに切り替え、アプリケーションにロールを割り当てる必要があります。

> [AZURE.NOTE] 特に証明書を認証に使用する場合に言えることですが、AD アプリケーションとサービス プリンシパルは、[PowerShell](resource-group-authenticate-service-principal.md) または [Azure CLI](resource-group-authenticate-service-principal-cli.md) の方が簡単に設定できる場合があります。このトピックでは、証明書の使用方法については説明していません。

Active Directory の概念については、「[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](./active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](./active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

## Active Directory アプリケーションを作成する

1. [クラシック ポータル](https://manage.windowsazure.com/)で Azure アカウントにログインします。

2. サブスクリプションの既定の Active Directory を確認してください。アクセス権を付与できるのは、該当するサブスクリプションと同じディレクトリ内のアプリケーションのみです。**[設定]** を選択し、サブスクリプションに関連付けられているディレクトリの名前を探します。詳細については、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](./active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
   
     ![既定のディレクトリの検索](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. 左側のペインで **[Active Directory]** を選択します。

     ![Active Directory の選択](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. アプリケーションの作成に使用する Active Directory を選択します。Active Directory が複数ある場合は、該当するサブスクリプションの既定のディレクトリにアプリケーションを作成します。

     ![ディレクトリの選択](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. ディレクトリ内のアプリケーションを表示するには、**[アプリケーション]** をクリックします。

     ![アプリケーションの表示](./media/resource-group-create-service-principal-portal/view-applications.png)

4. そのディレクトリにアプリケーションを作成したことがない場合、次の画像のように表示されます。**[アプリケーションの追加]** を選択します。

     ![アプリケーションの追加](./media/resource-group-create-service-principal-portal/create-application.png)

     または下のペインの **[追加]** をクリックします。

     ![add](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 作成するアプリケーションの種類を選択します。このチュートリアルでは、**[組織で開発中のアプリケーションを追加]** を選択します。

     ![新規アプリケーション](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. アプリケーションの名前を入力し、作成するアプリケーションの種類を選択します。このチュートリアルでは、**[Web アプリケーションや Web API]** を作成し、[次へ] をクリックします。**[ネイティブ クライアント アプリケーション]** を選択した場合、以降の手順が実際の画面と一致しなくなります。

     ![アプリケーションの名前指定](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. アプリのプロパティを入力します。**[サインオン URL]** には、アプリケーションについて説明する Web サイトの URI を指定します。Web サイトの存在は検証されません。**[アプリケーション ID/URI]** には、アプリケーションを識別する URI を指定します。

     ![アプリケーションのプロパティ](./media/resource-group-create-service-principal-portal/app-properties.png)

これでアプリケーションが作成されます。

## クライアント ID と認証キーを取得する

プログラムによってログインするときは、アプリケーションの ID が必要です。アプリケーションがその独自の資格情報で動作する場合は、さらに認証キーが必要となります。

1. **[構成]** タブを選択し、アプリケーションのパスワードを構成します。

     ![アプリケーションの構成](./media/resource-group-create-service-principal-portal/application-configure.png)

2. **[クライアント ID]** の値をコピーします。
  
     ![クライアント ID](./media/resource-group-create-service-principal-portal/client-id.png)

3. アプリケーションがその独自の資格情報で動作する場合は、下へスクロールして **[キー]** セクションを表示し、パスワードを有効にする期間を選択します。

     ![キー](./media/resource-group-create-service-principal-portal/create-key.png)

4. **[保存]** を選択してキーを作成します。

     ![保存](./media/resource-group-create-service-principal-portal/save-icon.png)

     保存されたキーが表示され、それをコピーすることができます。このキーは後で取得できないため、ここでコピーしてください。

     ![保存されたキー](./media/resource-group-create-service-principal-portal/save-key.png)

## テナント ID を取得する

プログラムによってログインするときは、認証要求と共にテナント ID を渡す必要があります。Web Apps と Web API Apps の場合、テナント ID を取得するには、画面の下部にある **[エンドポイントの表示]** を選択して、次の画像のように ID を取得します。

   ![テナント ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

次の PowerShell を使ってテナント ID を取得することもできます。

    Get-AzureRmSubscription

または、次の Azure CLI を使います。

    azure account show --json

## 委任されたアクセス許可を設定する

アプリケーションから、サインイン済みユーザーの代理でリソースにアクセスする場合、そのアプリケーションに対して、他のアプリケーションにアクセスできる委任されたアクセス許可を付与する必要があります。このアクセス許可は、**[構成]** タブの **[他のアプリケーションに対するアクセス許可]** で付与します。Azure Active Directory の既定では、委任されたアクセス許可は有効です。この委任されたアクセス許可は変更しないでください。

1. **[アプリケーションの追加]** をクリックします。

2. 一覧から **[Microsoft Azure サービス管理 API]** を選択します。次に、完了アイコンを選択します。

      ![アプリケーションの選択](./media/resource-group-create-service-principal-portal/select-app.png)

3. 委任されたアクセス許可のドロップダウン リストから **[Access Azure Service Management as organization (組織として Azure サービス管理にアクセスする)]** を選択します。

      ![アクセス許可の選択](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 変更を保存します。

## アプリケーションをロールに割り当てる

アプリケーションがその独自の資格情報で動作している場合は、アプリケーションをロールに割り当てる必要があります。アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断します。利用可能なロールについては、「[RBAC: 組み込みのロール](./active-directory/role-based-access-built-in-roles.md)」を参照してください。

アプリケーションにロールを割り当てるには適切なアクセス許可が必要です。具体的には、[所有者](./active-directory/role-based-access-built-in-roles.md#owner)ロールまたは[ユーザー アクセス管理者](./active-directory/role-based-access-built-in-roles.md#user-access-administrator)ロールを通じて付与される `Microsoft.Authorization/*/Write` アクセス権が必要です。共同作成者ロールには適切なアクセス権がありません。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。アクセス許可は、スコープの下位レベルに継承されます。たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます。

1. アプリケーションをロールに割り当てるには、クラシック ポータルから [Azure ポータル](https://portal.azure.com)に切り替えます。

1. サービス プリンシパルをロールに割り当てられるように、アクセス許可を確認します。アカウントの **[アクセス許可]** を選択します。

    ![select my permissions](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. アカウントの割り当て済みアクセス許可を表示します。既に説明したとおり、所有者ロールとユーザー アクセス管理者ロールのいずれかに属している必要があります。または、Microsoft.Authorization への書き込みアクセス権が付与される、カスタマイズされたロールが必要です。次の画像は、サブスクリプションの共同作成者ロールに割り当てられたアカウントを示します。これは、アプリケーションをロールに割り当てるうえで十分なアクセス許可ではありません。

    ![show my permissions](./media/resource-group-create-service-principal-portal/show-permissions.png)

     アプリケーションにアクセス権を付与するための適切なアクセス許可がない場合、ユーザー アクセス管理者ロールに自身を追加するようにサブスクリプション管理者に依頼するか、アプリケーションにアクセス権を付与するように管理者に依頼する必要があります。

1. アプリケーションを割り当てるスコープのレベルに移動します。このトピックでは、リソース グループに移動し、リソース グループ ブレードで **[アクセス制御]** を選択します。

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


ポータルを使用してユーザーやアプリケーションをロールに割り当てる方法の詳細については、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal)」を参照してください。

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

- セキュリティ ポリシーを指定する方法については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。
- これらの手順のビデオ デモについては、[Azure Active Directory を使用した Azure リソースのプログラムによる管理の有効化](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)に関するビデオを参照してください。

<!---HONumber=AcomDC_0824_2016-->