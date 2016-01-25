<properties 
	pageTitle="Azure App Service Web Apps への Java アプリケーションの追加" 
	description="このチュートリアルでは、Java を使用するように設定された Azure App Service Web Apps のインスタンスに、ページやアプリケーションを追加する方法について説明します。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Azure App Service Web Apps への Java アプリケーションの追加

「[Azure App Service での Java Web アプリの作成](web-sites-java-get-started.md)」の説明にあるように、Java Web アプリを [Azure App Service][] で初期設定した後、WAR を **webapps** フォルダーに配置してアプリケーションをアップロードすることができます。

**webapps** フォルダーへのナビゲーション パスは Web アプリのインスタンス設定方法によって異なります。

- Azure Marketplace を使用して Web アプリを設定した場合、**webapps** フォルダーへのパスは、**d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps** という形式になります (**application\_server** は、Web アプリのインスタンスで有効になっているアプリケーション サーバーの名前です)。 
- Azure の構成 UI を使用して Web アプリを設定した場合、**webapps** フォルダーへのパスは、**d:\\home\\site\\wwwroot\\webapps** という形式になります。 

継続的な統合シナリオなどで、ソース管理を使用してアプリケーションや Web ページをアップロードできます。Web アプリにソース管理を使用する手順は、「[Azure App Service の GIT を使用する継続的な展開](web-sites-publish-source-control.md)」で参照できます。FTP も、アプリケーションまたは Web ページをアップロードするためのオプションです。

WAR ファイルを **webapps** フォルダーにアップロードすると、Tomcat アプリケーション サーバーで WAR ファイルの追加が検出され、WAR ファイルが自動的に読み込まれます。ルート ディレクトリにファイル (WAR ファイル以外) をコピーした場合は、それらのファイルを使用する前に、アプリケーション サーバーの再起動が必要になります。Azure で実行されている Tomcat Java Web アプリの自動読み込み機能は、**webapps** フォルダーに追加される新しい WAR ファイル、または新しいファイルやディレクトリに基づいて動作します。

## 次のステップ

詳細については、[Java デベロッパー センター](/develop/java/)を参照してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=AcomDC_0114_2016-->