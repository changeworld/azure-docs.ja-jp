<properties
   pageTitle="Azure ポータルを使用した Azure サービス プリンシパルの新規作成"
   description="Azure リソース マネージャーでロール ベースのアクセス制御と共に使用してリソースへのアクセスを管理できる、新しい Azure サービス プリンシパルを作成する方法について説明します。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Azure ポータルを使用した Azure サービス プリンシパルの新規作成

## 概要
サービス プリンシパルは、他のリソースにアクセスする必要がある自動化されたプロセス、アプリケーション、またはサービスです。Azure リソース マネージャーを使用して、サービス プリンシパルにアクセス権限を付与し、それを認証して、サブスクリプション内に存在するリソースに対してまたはテナントとして、許可された管理操作を実行できるようにします。

このトピックでは、Azure ポータルを使用して、新しいサービス プロジェクトを作成する方法について説明します。現時点では、新しいサービス プリンシパルを作成するために Microsoft Azure ポータルを使用する必要があります。この機能は、今後のリリースで、Azure プレビュー ポータルに追加されます。

## 概念
1. Azure Active Directory (AAD) - クラウド用に構築された ID およびアクセス管理サービス。詳しくは、「[Azure Active Directory とは](./active-directory-whatis/)」を参照してください。
2. サービス プリンシパル - ディレクトリ内のアプリケーションのインスタンス。
3. AD アプリケーション - AAD に対してアプリケーションを特定する ADD 内のディレクトリ レコード。詳しくは、「[Azure AD での認証の基本](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)」を参照してください。


## Active Directory アプリケーションの作成
1. [従来のポータル](https://manage.windowsazure.com/)によって Azure アカウントにログインします。

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

## サービス プリンシパルのパスワードの作成
ポータルでは、ユーザーのアプリケーションが選択されているはずです。

1. **[構成]** タブをクリックして、 アプリケーションのパスワードを構成します。

   ![アプリケーションの構成][3]

2. 下にスクロールして **キー** セクションを表示し、パスワードを有効にする期間を選択します。

   ![キー][7]

3. **[保存]** を選択してキーを作成します。

   ![保存][13]

   保存されたキーが表示され、それをコピーすることができます。

   ![保存されたキー][8]

4. これで、サービス プリンシパルとして認証するためにそのキーを使用できます。サインインするためには、**キー**に加えて**クライアント ID** が必要です。**[クライアント ID]** に移動して、それをコピーしてください。
  
   ![クライアント ID][5]


これで、アプリケーションの準備が完了し、サービス プリンシパルがテナントに作成されました。サービス プリンシパルとしてサインインするときには、以下を使用してください。

* **クライアント ID** - ユーザー名として。
* **キー** - パスワードとして。

## 次のステップ
Getting Started (概要)

- [Azure リソース マネージャーの概要](./resource-group-overview.md)  
- [Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)
- [Azure リソース 管理での Azure CLI for Mac, Linux, and Windows の使用](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Azure ポータルを使用した Azure リソースの管理](./resource-group-portal.md)  
  
アプリケーションの作成と展開
  
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)  
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)  
- [Azure でのリソース グループのデプロイのトラブルシューティング](virtual-machines/resource-group-deploy-debug.md)  
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)  
- [高度なテンプレートの操作](./resource-group-advanced-template.md)  
- [.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ](virtual-machines/arm-template-deployment.md)
  
リソースの整理
  
- [タグを使用した Azure リソースの整理](./resource-group-using-tags.md)  
  
アクセスの管理と監査
  
- [リソースへのアクセスの管理と監査](./resource-group-rbac.md)  
- [Azure リソース マネージャーでのサービス プリンシパルの認証](./resource-group-authenticate-service-principal.md)  

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

<!--HONumber=52-->
