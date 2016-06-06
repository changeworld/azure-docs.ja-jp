<properties
	pageTitle="Azure Toolkit for Eclipse のインストール | Microsoft Azure"
	description="Azure Toolkit for Eclipse をインストールする方法について説明します。"
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
	ms.date="05/19/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Azure Toolkit for Eclipse のインストール

Azure Toolkit for Eclipse は、Eclipse 開発環境を使って Azure アプリケーションを簡単に作成、開発、テスト、またデプロイできるテンプレートと機能を提供します。Azure Toolkit for Eclipse はオープン ソース プロジェクトであり、そのソース コードは、GitHub のプロジェクト サイトから MIT License で入手できます。URL は次のとおりです。

<https://github.com/microsoft/azure-tools-for-java>

次の手順は、Azure Toolkit for Eclipse をインストールする方法を示しています。

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Azure Toolkit for Eclipse をインストールするには

1. Eclipse を起動します。

1. Eclipse の中で、次の図に示すように、**[Help]** メニューをクリックし、**[Install New Software]** をクリックします。

    ![Azure Toolkit for Eclipse のインストール][01]

1. **[Available Software]** ダイアログで、**[Work with]** ボックスに「**http://dl.microsoft.com/eclipse**」と入力し、**Enter** キーを押します。

1. **[Name]** ウィンドウで、**\[Azure Toolkit for Eclipse]** をオンにし、**[Contact all update sites during install to find required software]** をオフにします。画面は次のようになります。

    ![Azure Toolkit for Eclipse のインストール][02]

1. **Azure Toolkit for Eclipse** を展開すると、次の項目が表示されます。

    * **Application Insights Plugin for Java**: このコンポーネントによって、アプリケーションとサーバー インスタンスに対する Azure のテレメトリ ログと分析サービスを使用できます。
    * **Azure Access Control Services Filter**: Azure ACS を使用するアプリケーション ユーザーの認証を支援するコンポーネントです。シングル サインオンの利用形態が可能となり、ID のロジックをアプリケーションから切り離すことができます。
    * **Azure Common Plugin**: 他のツールキット コンポーネント全般で必要となる機能を備えたコンポーネントです。
    * **Azure Explorer for Eclipse**: 他のツールキット コンポーネント全般で必要となる機能を備えたコンポーネントです。
    * **Azure Plugin for Eclipse with Java**: Microsoft Azure クラウド向け Java アプリケーションを Eclipse やコマンド ラインによってビルド、テスト、デプロイできるプロジェクトの開発を支援するコンポーネントです。
    * **Azure Web Apps Plugin with Java**: Microsoft Azure Web アプリ コンテナーへの Java Web アプリケーションのデプロイを支援するコンポーネントです。
    * **Microsoft JDBC Driver 4.2 for SQL Server**: SQL Server と Microsoft Azure SQL Database 向けに、Java Platform Enterprise Edition 8 の JDBC API を提供するコンポーネントです。
    * **Package for Apache Qpid Client Libraries for JMS**: Apache Qpid プロジェクトの JMS クライアント ライブラリを備えたコンポーネントです。Microsoft Azure でアプリケーションから AMQP メッセージングを利用するには、このコンポーネントを使用します。
    * **Package for Microsoft Azure Libraries for Java**: Microsoft Azure の各種サービス (ストレージ、Service Bus、サービス ランタイムなど) にアクセスするための API を備えたコンポーネントです。

1. **[次へ]** をクリックします。(ツールキットのインストール時に異常な遅延が発生する場合は、**[Contact all update sites during install to find required software]** がオフになっていることを確認してください。)

1. **[Install Details]** ダイアログで、**[Next]** をクリックします。

    ![Review Installation Details][03]

1. **[Review Licenses]** ダイアログで、ライセンス契約の条項を確認します。ライセンス契約の条項に同意する場合は、**[I accept the terms of the license agreements]** をクリックし、**[Finish]** をクリックします。(残りの手順は、ライセンス契約の条項に同意したことを前提としています。ライセンス契約の条項に同意しない場合はインストール プロセスを終了します。)

    ![Review Licenses][04]

    必要なパッケージが Eclipse によってダウンロードされてインストールされます。

    ![Installation Progress][05]

1. インストールを完了するために Eclipse を再起動することを求めるメッセージが表示されたら、**[Yes]** をクリックします。

    ![Restart Prompt][06]

## 関連項目

[Azure Toolkit for Eclipse]

[Eclipse で Azure 用の Hello World Web アプリを作成する]

[Azure Toolkit for Eclipse の新機能]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!---HONumber=AcomDC_0525_2016-->