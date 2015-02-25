<properties 
	pageTitle="Web サイトの作成方法 - Azure サービス管理" 
	description="Azure の管理ポータルを使用して Web サイトを作成する方法について説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

#Web サイトを作成する方法

このトピックでは、管理ポータルを使用してギャラリーから Web サイトを作成する方法について説明します。

作成した Azure Web サイトにコンテンツをデプロイする方法については、「[Azure Websites](/ja-jp/documentation/services/web-sites/).」の「**展開**」セクションをご覧ください。

## 目次 ##

- [方法:管理ポータルの使用による Web サイトの作成](#createawebsiteportal)
- [方法:ギャラリーからの Web サイトの作成](#howtocreatefromgallery)
- [方法:Web サイトの削除](#deleteawebsite)
- [次のステップ](#nextsteps)

##<a name="createawebsiteportal"></a>方法:管理ポータルの使用による Web サイトの作成

Azure で Web サイトを作成するには、次の手順に従います。
	
1. [Azure 管理ポータル](http://manage.windowsazure.com/) にログインします。

2. 管理ポータルの左下にある **[新規作成]** アイコンをクリックします。

3. **[Web サイト]** アイコン、**[簡易作成]** アイコンの順にクリックし、URL の値を入力して、ページの右下にある **[Web サイトの作成]** チェック マークをクリックします。

4. Web サイトが作成されると、**"Web サイト <*サイト名*> の作成に成功しました"**というテキストが表示されます。ポータル ページの下部にある **[参照]** をクリックすると、Web サイトを参照できます。

5. ポータルで、Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトの **[クイック スタート]** 管理ページを開きます。

6. **[クイック スタート]** ページには、Web サイト開発ツールの入手、Web サイトの発行の設定、TFS や Git のようなソース管理プロバイダーからのデプロイの設定を行うためのオプションがあります。FTP 発行は Web サイトに対して既定で設定されており、**[ダッシュボード]** ページの **[概要]** セクションの **[FTP ホスト名]** に FTP ホスト名が表示されます。FTP または Git を使って発行する前に、**[ダッシュボード]** ページで **[デプロイ資格情報のリセット]** を選択して、Web サイトにコンテンツをデプロイするときに FTP ホストまたは Git リポジトリに対して認証できるようにします。

7. **[構成]** 管理ページでは、次のカテゴリについて Web サイトの設定が表示されます。

	- Web アプリケーションに必要な .NET Framework または PHP のバージョンを設定します。
	- SSL バインド
	- カスタム ドメイン名
	- ログ オプション
	- Azure 環境のアプリケーション設定 (現在の開発環境の Web.config 内の <appSettings> の上書きなど)
	- 接続文字列 (現在の開発環境の Web.config 内の <connectionStrings> の上書きなど)
	- *.php など、特定のファイル拡張子に対するスクリプト プロセッサ

##<a name="howtocreatefromgallery"></a>方法:ギャラリーからの Web サイトの作成

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>方法:Web サイトの削除
Web サイトは、Azure の管理ポータルの **[削除]** アイコンを使用して削除します。**[削除]** アイコンは、Azure ポータル (**[Web サイト]** をクリックして Web サイトをすべて表示したとき) と各 Web サイトの管理ページの下部に表示されます。

##<a name="nextsteps"></a>次のステップ

詳細については、[Azure Websites のページ](/ja-jp/documentation/services/web-sites/).をご覧ください。


<!--HONumber=42-->
