<properties 
	pageTitle="IntelliJ で Azure の Java Web アプリをデバッグする | Microsoft Azure" 
	description="このチュートリアルでは、Azure Toolkit for IntelliJ を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。" 
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

# IntelliJ で Azure の Java Web アプリをデバッグする

このチュートリアルでは、[Azure Toolkit for IntelliJ] を使用して、Azure で実行中の Java Web アプリをデバッグする方法について説明します。わかりやすくするために、このチュートリアルでは基本的な Java Server Page (JSP) サンプルを使用しますが、Azure でのデバッグであれば Java サーブレットを使用した場合も同様の手順になります。

IntelliJ でデバッグする場合、このチュートリアルの完了時には、アプリケーションが次のような画面に表示された状態になります。

![][01]
 
## 前提条件

* Java Developer Kit (JDK) v 1.8 以降。
* IntelliJ IDEA Ultimate エディション。<https://www.jetbrains.com/idea/download/index.html> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、Jetty など)。
* Azure サブスクリプション。<https://azure.microsoft.com/free/> または <http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* Azure Toolkit for IntelliJ。詳細については、「[Azure Toolkit for IntelliJ のインストール]」を参照してください。
* 作成して Azure App Service にデプロイした Dynamic Web Project。「[IntelliJ で Azure 用の Hello World Web アプリを作成する]」の例を参照してください。

## Azure の Java Web アプリをデバッグするには

このセクションの各手順を実行する際は、Azure に Java Web アプリとしてデプロイした既存の Dynamic Web Project を使用するか、[サンプル Dynamic Web Project] をダウンロードし、「[IntelliJ で Azure 用の Hello World Web アプリを作成する]」の手順に従って Azure にデプロイして使用することができます。

1. Azure にデプロイした Java Web アプリ プロジェクトを IntelliJ で開きます。

1. **[Run (実行)]** メニューをクリックし、**[Edit Configurations... (構成の編集)]** をクリックします。

    ![][02]

1. **[Run/Debug Configurations (実行/デバッグ構成)]** ダイアログ ボックスが開いたら、次の手順を実行します。

    1. **[Azure Web App (Azure Web アプリ)]** を選択します。
    1. **[+]** をクリックして新しい構成を追加します。
    1. 構成の **[Name (名前)]** を入力します。
    1. Azure Toolkit によって入力されている残りの既定値をそのまま使用し、**[OK]** をクリックします。

        ![][03]

1. メニューの右上で、作成した Azure Web アプリのデバッグ構成を選択し、**[Debug (デバッグ)]** をクリックします。

    ![][04]

1. **[Enable remote debugging in the remote Web App now? (リモート Web アプリで今すぐリモート デバッグを有効にしますか?)]** というメッセージが表示されたら、**[OK]** をクリックします。

1. **[Your web app is now ready for remote debugging (Web アプリでリモート デバッグの準備ができました)]** というメッセージが表示されたら、**[OK]** をクリックします。

    ![][05]

1. メニューの右上で、作成した Azure Web アプリのデバッグ構成を選択し、**[Debug (デバッグ)]** をクリックします。

1. Windows コマンド プロンプトまたは Unix shell が開き、デバッグに必要な接続が用意されます。リモート Java Web アプリへの接続に成功するまで、先へ進むことはできません。Windows を使用している場合は、次のような画面表示になります。

    ![][06]

1. JSP ページにブレーク ポイントを挿入し、Java Web アプリの URL をブラウザーで開きます。

    1. IntelliJ で **[Azure Explorer]** を開きます。
    1. **[Web Apps]** に移動し、デバッグする Web アプリに移動します。
    1. Web アプリを右クリックし、**[Open in Browser (ブラウザーで開く)]** をクリックします。
    1. これで、IntelliJ がデバッグ モードに切り替わります。

## 次のステップ

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

Azure Web Apps の作成の詳細については、「[Web Apps の概要]」を参照してください。

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij.md
[Azure Toolkit for IntelliJ のインストール]: ../azure-toolkit-for-intellij-installation.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web-intellij-create-hello-world-web-app.md
[サンプル Dynamic Web Project]: http://go.microsoft.com/fwlink/?LinkId=817337

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Web Apps の概要]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->