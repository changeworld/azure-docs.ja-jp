<properties 
	pageTitle="Eclipse で Azure の Java Web アプリをデバッグする | Microsoft Azure" 
	description="このチュートリアルでは、Azure Toolkit for Eclipse を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="asirveda;robmcm"/>

# Eclipse で Azure の Java Web アプリをデバッグする

このチュートリアルでは、[Azure Toolkit for Eclipse] を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。わかりやすくするために、このチュートリアルでは基本的な Java Server Page (JSP) サンプルを使用しますが、Azure でのデバッグであれば Java サーブレットを使用した場合も同様の手順になります。

Eclipse でデバッグする場合、このチュートリアルの完了時には、アプリケーションが次のような画面に表示された状態になります。

![][01]
 
## 前提条件

* Java Developer Kit (JDK) v 1.8 以降。
* Eclipse IDE for Java EE Developers Indigo 以降。<http://www.eclipse.org/downloads/> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、Jetty など)。
* Azure サブスクリプション。<https://azure.microsoft.com/free/> または <http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* Azure Toolkit for Eclipse。詳細については、「[Azure Toolkit for Eclipse のインストール]」を参照してください。
* 作成して Azure App Service にデプロイした Dynamic Web Project。「[Eclipse で Azure 用の Hello World Web アプリを作成する]」の例を参照してください。

## Azure の Java Web アプリをデバッグするには

このセクションの各手順を実行する際は、Azure に Java Web アプリとしてデプロイした既存の Dynamic Web Project を使用するか、[サンプル Dynamic Web Project] をダウンロードし、「[Eclipse で Azure 用の Hello World Web アプリを作成する]」の手順に従って Azure にデプロイして使用することができます。

1. Eclipse を開きます。

1. リモート デバッグ用のタイムアウトを構成します。

    1. Eclipse で **[Window (ウィンドウ)]** メニューをクリックし、**[Preferences (ユーザー設定)]** をクリックします。
    1. **[Java]** ノードを展開し、**[Debug (デバッグ)]** を選択します。
    1. **[Debugger timeout (ms) (デバッガー タイムアウト (ミリ秒))]** と **[Launch timeout (ms) (起動タイムアウト (ミリ秒))]** の各設定を両方とも `120000` にします。

        ![][02]

    1. **[OK]** をクリックして **[Preferences (ユーザー設定)]** ダイアログ ボックスを閉じます。

1. Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ビューで、Azure にデプロイした Dynamic Web Project を右クリックします。コンテキスト メニューが表示されたら、**[Debug As (デバッグ形式)]** を選択し、**[Azure Web App (Azure Web アプリ)]** をクリックします。

    ![][03]

1. Dynamic Web Project を初めてデバッグする場合は、**[Debug Configurations (デバッグ構成)]** ダイアログ ボックスが開きます。Toolkit によって **[Connect (接続)]** タブで指定されている既定値はそのまま使用できます。**[Source (ソース)]** タブで **[Add (追加)]**、**[Java project (Java プロジェクト)]** の順にクリックし、**[Dynamic Web Project]** を選択して **[OK]** をクリックします。これらの手順を最後まで実行したら、**[Debug (デバッグ)]** をクリックします。

    ![][04]

1. **[Enable remote debugging in the remote Web App now? (リモート Web アプリで今すぐリモート デバッグを有効にしますか?)]** というメッセージが表示されたら、**[OK]** をクリックします。

1. **[Your web app is now ready for remote debugging (Web アプリでリモート デバッグの準備ができました)]** というメッセージが表示されたら、**[OK]** をクリックします。

    ![][05]

1. **[Debug Configurations (デバッグ構成)]** ダイアログ ボックスが再度表示されたら、**[Debug (デバッグ)]** をクリックします。

1. Windows コマンド プロンプトまたは Unix shell が開き、デバッグに必要な接続が用意されます。リモート Java Web アプリへの接続に成功するまで、先へ進むことはできません。Windows を使用している場合は、次のような画面表示になります。

    ![][06]

1. JSP ページにブレーク ポイントを挿入し、Java Web アプリの URL をブラウザーで開きます。

    1. Eclipse で **[Azure Explorer]** を開きます。
    1. **[Web Apps]** に移動し、デバッグする Web アプリに移動します。
    1. Web アプリを右クリックし、**[Open in Browser (ブラウザーで開く)]** をクリックします。
    1. これで、Eclipse がデバッグ モードに切り替わります。

## 次のステップ

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

Azure Web Apps の作成の詳細については、「[Web Apps の概要]」を参照してください。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit for Eclipse のインストール]: ../azure-toolkit-for-eclipse-installation.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web-eclipse-create-hello-world-web-app.md
[サンプル Dynamic Web Project]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Web Apps の概要]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->