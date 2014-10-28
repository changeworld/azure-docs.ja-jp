<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java website" pageTitle="Add an application to your Java website" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java website on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Azure の Java Web サイトへのアプリケーションの追加

「[Microsoft Azure Web サイトと Java の概要][Microsoft Azure Web サイトと Java の概要]」の説明にあるように Java Web サイトを初期設定した後、WAR を **webapps** フォルダーに配置してアプリケーションをアップロードすることができます。

**webapps** フォルダーへのナビゲーション パスは Web サイトの設定方法によって異なります。

-   Azure のアプリケーション ギャラリーを使用して Web サイトを設定した場合、**webapps** フォルダーへのパスは、**d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps** という形式になります (**application\_server** は、Web サイトで有効になっているアプリケーション サーバーの名前です)。
-   Azure の構成 UI を使用して Web サイトを設定した場合、**webapps** フォルダーへのパスは、**d:\\home\\site\\wwwroot\\webapps** という形式になります。

継続的な統合シナリオなどで、ソース管理を使用してアプリケーションや Web ページをアップロードできます。Web サイトでのソース管理の使用手順については、「[ソース管理から Azure Web サイトへの発行][ソース管理から Azure Web サイトへの発行]」を参照してください。FTP も、アプリケーションまたは Web ページをアップロードするためのオプションです。

Tomcat Web サイトに関する注意: WAR ファイルを **webapps** フォルダーにアップロードすると、Tomcat アプリケーション サーバーで WAR ファイルの追加が検出され、WAR ファイルが自動的に読み込まれます。ルート ディレクトリにファイル (WAR ファイル以外) をコピーした場合は、それらのファイルを使用する前に、アプリケーション サーバーの再起動が必要になります。Azure で実行されている Tomcat Java Web サイトの自動読み込み機能は、**webapps** フォルダーに追加される新しい WAR ファイル、または新しいファイルやディレクトリに基づいて動作します。

  [Microsoft Azure Web サイトと Java の概要]: ../web-sites-java-get-started
  [ソース管理から Azure Web サイトへの発行]: ../web-sites-publish-source-control
