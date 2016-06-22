<properties
	pageTitle="Azure Toolkit for IntelliJ のインストール | Microsoft Azure"
	description="Azure Toolkit for IntelliJ IDEA をインストールする方法について説明します。"
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/07/2016" 
	ms.author="robmcm"/>

# Azure Toolkit for IntelliJ のインストール

Azure Toolkit for IntelliJ は、IntelliJ IDEA 開発環境を使って Azure アプリケーションを簡単に作成、開発、テスト、デプロイできるテンプレートと機能を提供します。Azure Toolkit for IntelliJ はオープン ソース プロジェクトであり、そのソース コードは、GitHub のプロジェクト サイトから MIT License で入手できます。URL は次のとおりです。

<https://github.com/microsoft/azure-tools-for-java>

Azure Toolkit for IntelliJ には 2 とおりのインストール方法があります。[Settings] \(設定) ダイアログ ボックスを使用する方法と、スタート画面の [Configure] \(構成) メニューを使用する方法です。その両方のインストール方法を以降の手順で説明します。

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## Azure Toolkit for IntelliJ を [Settings] \(設定) ダイアログ ボックスからインストールするには

1. IntelliJ IDEA を起動します。

1. IntelliJ IDEA が起動したら **[File]** (ファイル) をクリックし、**[Settings]** (設定) をクリックします。

    ![Open the IntelliJ IDEA Settings Dialog Box][01a]

1. [Settings] \(設定) ダイアログ ボックスの **[Plugins]** \(プラグイン) をクリックし、**[Browse repositories]** \(リポジトリの参照) をクリックします。

    ![IntelliJ IDEA Settings Dialog Box][02a]

1. **[Browse Repositories]** (リポジトリの参照) ダイアログ ボックスの検索ボックスに「Azure」と入力します。**\[Azure Toolkit for IntelliJ]** を強調表示して **[Install]** (インストール) をクリックします。

    ![Search for the Azure Toolkit for IntelliJ][03]

    インストールの進行状況がダイアログ ボックスに表示されます。

    ![Installation progress][04]

1. インストールが完了したら、**[Restart IntelliJ IDEA]** (IntelliJ IDEA の再起動) をクリックします。

    ![Restart IntelliJ IDEA][05]

1. **[OK]** をクリックして [Settings] (設定) ダイアログ ボックスを閉じます。

    ![Close IntelliJ IDEA Settings Dialog Box][06]

1. IntelliJ IDEA をすぐに再起動するか、後から再起動するかを求められたら、**[Restart]** (再起動) をクリックします。

    ![Restart IntelliJ IDEA][07]

## Azure Toolkit for IntelliJ をスタート画面からインストールするには

1. IntelliJ IDEA を起動します。

1. IntelliJ IDEA のスタート画面が表示されたら、**[Configure]** (構成) をクリックし、**[Plugins]** (プラグイン) をクリックします。

    ![Install IntelliJ IDEA Plugins][01b]

1. **[Plugins]** (プラグイン) ダイアログ ボックスで **[Browse repositories]** (リポジトリの参照) をクリックします。

    ![Browse IntelliJ IDEA Plugin Repositories][02b]

1. **[Browse Repositories]** (リポジトリの参照) ダイアログ ボックスの検索ボックスに「Azure」と入力します。**[Azure Toolkit for IntelliJ]** を強調表示して **[Install]** (インストール) をクリックします。

    ![Search for the Azure Toolkit for IntelliJ][03]

    インストールの進行状況がダイアログ ボックスに表示されます。

    ![Installation progress][04]

1. インストールが完了したら、**[Restart IntelliJ IDEA]** (IntelliJ IDEA の再起動) をクリックします。

    ![Restart IntelliJ IDEA][05]

1. IntelliJ IDEA をすぐに再起動するか、後から再起動するかを求められたら、**[Restart]** (再起動) をクリックします。

    ![Restart IntelliJ IDEA][07]

## 関連項目

Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

- [Azure Toolkit for Eclipse]
  - [Azure Toolkit for Eclipse のインストール]
  - [Eclipse で Azure 用の Hello World Web アプリを作成する]
  - [Azure Toolkit for Eclipse の新機能]
- [Azure Toolkit for IntelliJ]
  - *Azure Toolkit for IntelliJ のインストール (この記事)*
  - [IntelliJ で Azure 用の Hello World Web アプリを作成する]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png

<!---HONumber=AcomDC_0608_2016-->