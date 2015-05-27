<properties 
	pageTitle="Azure Marketplace から Orchard CMS Web アプリを作成する" 
	description="Azure で新しい Web アプリを作成する方法について説明するチュートリアルです。また、Azure ポータルを使用して Web アプリを公開し、管理する方法についても説明しています。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Azure Marketplace から Orchard CMS Web アプリを作成する

## 概要

Marketplace には、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリケーションが用意されています。Marketplace から作成した Web アプリケーションは、[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)への接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。Marketplace 内の Web アプリケーションの詳細については、「[Azure Marketplace](/marketplace/)」を参照してください。

このチュートリアルでは、次のことについて説明します。

- Marketplace から新しい Web アプリを作成する方法

- Azure ポータルから Web アプリを公開して管理する方法
 
既定のテンプレートを使用する Orchard CMS Web アプリを構築します。[Orchard](http://www.orchardproject.net/) はオープン ソースで .NET ベースの無料 CMS アプリケーションで、カスタマイズしたコンテンツ主導の Web アプリおよび Web サイトを作成できます。Orchard CMS には、[追加のモジュールとテーマをダウンロード](http://gallery.orchardproject.net/)して Web アプリをカスタマイズできる機能拡張フレームワークが付属しています。次の図は、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にこれから作成する Orchard CMS Web アプリを示しています。

![Orchard ブログ][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## Marketplace から Orchard Web アプリを作成する

1. [Azure プレビュー ポータル](http://portal.azure.com)にログインします。

2. **[新規]**、**[Web + モバイル]**、**[Azure Marketplace]** の順にクリックします。
	
	![Create New][1]

3. **[Web Apps]**、**[Orchard CMS]** の順にクリックします。次のブレードで **[作成]** をクリックします。
	
	![Create From Marketplace][2]

4. Web アプリの URL、App Service プラン、リソース グループ、および場所を構成します。完了したら、**[作成]** をクリックします。
	
	![Configure the app][3]

	Web アプリが作成されたら、**[通知]** ボタンに緑の "成功" が表示され、Web アプリのブレードが表示されます。

## Orchard サイトを起動して管理する

1. Web アプリのブレードで **[参照]** をクリックして、Web アプリのウェルカム ページを開きます。

	![[参照] ボタン][12]

2. Orchard に必要となる構成情報を入力します。次に、**[セットアップの終了]** をクリックして構成を完了し、Web アプリのホーム ページを開きます。

	![Orchard へのログイン][7]

	新しい Orchard Web アプリは次のスクリーンショットのようになります。

	![your Orchard web app][13]

3. Orchard の詳細と新しい Web アプリの構成方法については、[Orchard のドキュメント](http://docs.orchardproject.net/)の記述に従ってください。

## 次のステップ

* [Microsoft WebMatrix を使用して Web アプリを開発してデプロイする](web-sites-dotnet-using-webmatrix.md) -- WebMatrix で App Service の Web アプリを編集する方法について説明します。 
* [認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) -- Visual Studio から Azure App Service で新しい Web アプリを作成する方法について説明します。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* ポータルからプレビュー ポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png



<!--HONumber=54-->