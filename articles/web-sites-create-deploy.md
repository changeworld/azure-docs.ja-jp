<properties
	pageTitle="Web アプリの作成方法 - Azure サービス管理"
	description="Azure ポータルを使用して Web アプリを作成する方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#Web アプリの作成方法

このトピックでは、Azure ポータルを使用してギャラリーから Web アプリを作成する方法について説明します。

作成した Web アプリにコンテンツをデプロイする方法については、「[Azure Web アプリ](http://go.microsoft.com/fwlink/?LinkId=529714)」の「**デプロイ**」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[アプリ サービスの試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。アプリ サービスで、有効期間が短いスターター Web アプリをすぐに作成できます。クレジット カードが必要なコミットメントはありません。

##<a name="createawebsiteportal"></a>方法:Azure ポータルを使用して web アプリを作成する

Azure で Web アプリを作成するには、次のステップに従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)にログインします。

2. Azure ポータルの左下にある **[新規]** アイコンをクリックします。

3. **[Web + モバイル]** アイコン、**[Web アプリ]** アイコンの順にクリックし、URL の値を入力して、[作成] ブレードの右下にある **[作成]** をクリックします。

4. Web アプリが作成されると、「**Deployment to resource group <resouce group name> was successful (リソース グループ <リソース グループ名> へのデプロイが成功しました)**」という文が表示されます。

5. ポータルで、Web アプリの一覧に表示されている Web アプリの名前をクリックして、Web アプリのブレードを開きます。

6. このブレードには、Web アプリ開発ツールの入手、Web アプリの発行の設定、TFS や Git のようなソース管理プロバイダーからのデプロイの設定を行うためのオプションがあります。FTP 発行は Web アプリに対して既定で設定されており、[Web アプリ] ブレードの **[Essentials]** セクションに FTP ホスト名が表示されます。FTP または Git を使って発行する前に、[Web アプリ] ブレードで **[発行プロファイルのリセット]** を選択して、Web アプリにコンテンツをデプロイするときに FTP ホストまたは Git リポジトリに対して認証できるようにします。

7. **[設定]** ブレードには、次のような Web アプリの設定が表示されます。

	- web アプリの .NET、PHP、Java、Python のバージョン
	- Visual Studio Online での編集
	- SSL バインド
	- カスタム ドメイン名
	- 認証と承認
	- アプリケーション診断とサイト診断
	- エンドポイントの監視
	- ログ オプション
	- Azure 環境向けのアプリ設定 (現在の開発環境の Web.config 内の <appSettings> の上書きなど)
	- 接続文字列 (現在の開発環境の Web.config 内の <connectionStrings> の上書きなど)
	- *.php など、特定のファイル拡張子に対するスクリプト プロセッサ

##<a name="howtocreatefromgallery"></a> 方法: ギャラリーからの Web アプリの作成

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a> 方法: Web アプリの削除
Web アプリは、Azure ポータルの **[削除]** アイコンを使用して削除します。**[削除]** アイコンは、[Web アプリ] ブレードの上部にあります。

##<a name="nextsteps"></a> 次のステップ

詳細については、「[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

## 変更内容
* Web サイトからアプリ サービスへの変更に関するガイドについては、以下を参照してください。[Azure App Service and Its Impact on Existing Azure Services (Azure アプリ サービスと既存の Azure サービスへの影響)](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイドについては、以下を参照してください。[Reference for navigating the preview portal (プレビュー ポータルをナビゲートするためのリファレンス)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->