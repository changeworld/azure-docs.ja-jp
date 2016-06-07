<properties
   pageTitle="Active Directory アプリケーションをポータルで作成する | Microsoft Azure"
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
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# リソースにアクセスできる Active Directory アプリケーションをポータルで作成する

自動化されたプロセスやアプリケーションでリソースにアクセスしたり変更を加えたりするには、Active Directory アプリケーションをセットアップして、そこに必要な権限を割り当てる必要があります。このトピックでは、それらの手順をポータルで行う方法について説明します。現時点では、クラシック ポータルを使用して新しい Active Directory アプリケーションを作成したうえで、Azure ポータルに切り替え、アプリケーションにロールを割り当てる必要があります。

Active Directory アプリケーションに使用する認証方法は、次の 2 つの中から選ぶことができます。

1. アプリケーションの ID と認証キーを作成し、アプリケーションの実行時にそれらの資格情報を付与する。ユーザーの介入なしで実行される自動化されたプロセスにはこの方法を使用します。
2. アプリケーションを介してユーザーが Azure にログインし、アプリケーションがそれらの資格情報を使用して、ユーザーの代わりにリソースにアクセスできるようにする。ユーザーによって実行されるアプリケーションには、この方法を使用します。

Active Directory の概念については、「[アプリケーション オブジェクトおよびサービス プリンシパル オブジェクト](./active-directory/active-directory-application-objects.md)」を参照してください。Active Directory 認証の詳細については、「[Azure AD の認証シナリオ](./active-directory/active-directory-authentication-scenarios.md)」をご覧ください。

アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

## Active Directory アプリケーションを作成する

1. [従来のポータル](https://manage.windowsazure.com/)によって Azure アカウントにログインします。

2. 左側のペインで **[Active Directory]** を選択します。

     ![Active Directory の選択](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. 新しいアプリケーションの作成に使用する Active Directory を選択します。複数の Active Directory がある場合は、通常、該当するサブスクリプションが存在するディレクトリにアプリケーションを作成します。アクセス権を付与できるのは、該当するサブスクリプション内のリソースだけであり、該当するサブスクリプションと同じディレクトリ内のアプリケーションのリソースが対象となります。

     ![ディレクトリの選択](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    サブスクリプションのディレクトリを探す場合は、**[設定]** を選択し、ディレクトリ名を検索します。
   
     ![既定のディレクトリの検索](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. ディレクトリ内のアプリケーションを表示するには、**[アプリケーション]** をクリックします。

     ![アプリケーションの表示](./media/resource-group-create-service-principal-portal/view-applications.png)

4. そのディレクトリにアプリケーションを作成したことがない場合、次の図のようなものが表示されます。**[アプリケーションの追加]** をクリックします。

     ![アプリケーションの追加](./media/resource-group-create-service-principal-portal/create-application.png)

     または下のペインの **[追加]** をクリックします。

     ![追加](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 作成するアプリケーションの種類を選択します。このチュートリアルでは、**[組織で開発中のアプリケーションを追加]** を選択します。

     ![新規アプリケーション](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. アプリケーションの名前を入力し、作成するアプリケーションの種類を選択します。このチュートリアルでは、**[Web アプリケーションや Web API]** を作成し、[次へ] をクリックします。**[ネイティブ クライアント アプリケーション]** を選択した場合、以降の手順が実際の画面と一致しなくなります。

     ![アプリケーションの名前指定](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. アプリのプロパティを入力します。**[サインオン URL]** には、アプリケーションについて説明する Web サイトの URI を指定します。Web サイトの存在は検証されません。**[アプリケーション ID/URI]** には、アプリケーションを識別する URI を指定します。

     ![アプリケーションのプロパティ](./media/resource-group-create-service-principal-portal/app-properties.png)

これでアプリケーションが作成されます。

## クライアント ID と認証キーを取得する

プログラムによってログインするときは、アプリケーションの ID が必要です。アプリケーションがその独自の資格情報で動作する場合は、さらに認証キーが必要となります。

1. **[構成]** タブをクリックして、 アプリケーションのパスワードを構成します。

     ![アプリケーションの構成](./media/resource-group-create-service-principal-portal/application-configure.png)

2. **クライアント ID** をコピーします。
  
     ![クライアント ID](./media/resource-group-create-service-principal-portal/client-id.png)

3. アプリケーションがその独自の資格情報で動作する場合は、下にスクロールして**キー** セクションを表示し、パスワードを有効にする期間を選択します。

     ![キー](./media/resource-group-create-service-principal-portal/create-key.png)

4. **[保存]** を選択してキーを作成します。

     ![保存](./media/resource-group-create-service-principal-portal/save-icon.png)

     保存されたキーが表示され、それをコピーすることができます。キーは後からは取得できないため、今すぐコピーしてください。

     ![保存されたキー](./media/resource-group-create-service-principal-portal/save-key.png)

## テナント ID を取得する

プログラムによってログインするときは、認証要求と共にテナント ID を渡す必要があります。Web Apps および Web API Apps の場合、テナント ID を取得するには、画面の下部にある **[エンドポイントの表示]** を選択して、次のように ID を取得します。

   ![テナント ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

次の PowerShell を使ってテナント ID を取得することもできます。

    Get-AzureRmSubscription

または、次の Azure CLI を使います。

    azure account show --json

## 委任されたアクセス許可を設定する

アプリケーションから、サインイン済みユーザーの代理でリソースにアクセスする場合、そのアプリケーションに対して、他のアプリケーションにアクセスできる委任されたアクセス許可を付与する必要があります。この設定は、**[構成]** タブの **[他のアプリケーションに対するアクセス許可]** で行います。Azure Active Directory の既定では、委任されたアクセス許可は有効です。この委任されたアクセス許可は変更しないでください。

1. **[アプリケーションの追加]** をクリックします。

2. 一覧から **[Azure Service Management API]** を選択します。次に、完了アイコンを選択します。

      ![アプリケーションの選択](./media/resource-group-create-service-principal-portal/select-app.png)

3. 委任されたアクセス許可のドロップダウン リストから **[Access Azure Service Management as organization]** (組織として Azure サービス管理にアクセスする) を選択します。

      ![アクセス許可の選択](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 変更を保存します。

## マルチテナント アプリケーションを構成する

他の Azure Active Directory のユーザーがアプリケーションに同意してサインインできるようにするには、マルチテナント機能を有効にする必要があります。**[構成]** タブで、**[アプリケーションはマルチテナントです]** を **[はい]** に設定します。

![マルチテナント](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## アプリケーションをロールに割り当てる

アプリケーションがその独自の資格情報で動作している場合は、アプリケーションをロールに割り当てる必要があります。アプリケーションにとって適切なアクセス許可を表すのはどのロールであるかを判断する必要があります。利用可能なロールについては、「[RBAC: 組み込みのロール](./active-directory/role-based-access-built-in-roles.md)」を参照してください。

スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。アクセス許可は、スコープの下位レベルに継承されます (たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます)。

1. アプリケーションをロールに割り当てるには、クラシック ポータルから [Azure ポータル](https://portal.azure.com)に切り替えます。

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

Active Directory アプリケーションからリソースにアクセスするための構成は以上で完了です。アプリケーションで資格情報を入力すると、アクセス トークンが与えられます。リソースにアクセスする要求には、このアクセス トークンを使用します。

プログラムでアプリケーションにログインすることができます。

- .NET の例については、「[Azure Resource Manager SDK for .NET](resource-manager-net-sdk.md)」を参照してください。
- Java の例については、「[Azure Resource Manager SDK for Java](resource-manager-java-sdk.md)」を参照してください。
- Python の例については、「[Resource Management Authentication for Python (Python 向けリソース管理認証)](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)」を参照してください。
- REST の例については、「[Resource Manager REST API](resource-manager-rest-api.md)」を参照してください。

アプリケーションを Azure に統合してリソースを管理する詳しい手順については、「[Azure Resource Manager API を使用した承認の開発者ガイド](resource-manager-api-authentication.md)」を参照してください。

## 次のステップ

- セキュリティ ポリシーを指定する方法については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。  
- これらの手順のビデオ デモについては、[Azure Active Directory を使用した Azure リソースのプログラムによる管理の有効化](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)に関するビデオを参照してください。

<!---HONumber=AcomDC_0525_2016-->