<properties
	pageTitle="Azure Toolkit for Eclipse のインストール"
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
	ms.date="03/09/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Azure Toolkit for Eclipse のインストール

Azure Toolkit for Eclipse は、Eclipse 開発環境を使って Azure アプリケーションを簡単に作成、開発、テスト、またデプロイできるテンプレートと機能を提供します。これはオープン ソース プロジェクトであり、そのソース コードは、GitHub のプロジェクト サイトから Apache License 2.0 で入手できます。URL は次のとおりです。

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>

次の手順は、Azure Toolkit for Eclipse をインストールする方法を示しています。

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Azure Toolkit for Eclipse をインストールするには

1. Eclipse を起動します。
2. Eclipse の中で、次の図に示すように、<strong>[Help]</strong> メニューをクリックし、<strong>[Install New Software]</strong> をクリックします。![Azure Toolkit for Eclipse のインストール][ic590123]
3. <strong>[Available Software]</strong> ダイアログで、<strong>[Work with]</strong> ボックスに「<strong>http://dl.microsoft.com/eclipse</strong>」と入力し、<strong>Enter</strong> キーを押します。
4. <strong>[Name]</strong> ウィンドウで、<strong>[Azure Toolkit for Eclipse]</strong> をオンにし、<strong>[Contact all update sites during install to find required software]</strong> をオフにします。画面は次のようになります。![Azure Toolkit for Eclipse のインストール][ic719482]
5. <strong>Azure Toolkit for Eclipse</strong> を展開すると、次の項目が表示されます。
    * **Azure Access Control Services Filter**: このコンポーネントは、Azure ACS でのアプリケーション ユーザーの認証をサポートします。
    * **Azure Common Plugin**: このコンポーネントには、他のコンポーネントが依存する共有機能が含まれています。
    * **Azure Toolkit for Eclipse**: このコンポーネントには、プロジェクト構成ロジック、クラウドへの発行ウィザード、およびユーザー インターフェイスが含まれています。
    * **Microsoft JDBC Driver 4.0 for SQL Server**: このコンポーネントは、SQL Database を使用したアプリケーション開発を簡略化します。
    * **Package for Apache Qpid Client Libraries for JMS**: このコンポーネントは、アプリケーションが Azure 内で Advanced Messaging Queuing Protocol (AMQP) ベースのメッセージングを使用できるようにする、Apache Qpid プロジェクトの JMS クライアント ライブラリを提供します。
    * **Package for Azure Libraries for Java**: このコンポーネントによって、Azure のスケーラブルなクラウド コンピューティング リソースを活用できる Azure アプリケーションを Java でビルドできます。
    * **Application Insights Plugin for Java**: このコンポーネントによって、アプリケーションとサーバー インスタンスに対する Azure のテレメトリ ログと分析サービスを使用できます。
6. **[次へ]** をクリックします。(ツールキットのインストール時に異常な遅延が発生する場合は、**[Contact all update sites during install to find required software]** がオフになっていることを確認してください。)
7. **[Install Details]** ダイアログで、**[Next]** をクリックします。
8. **[Review Licenses]** ダイアログで、ライセンス契約の条項を確認します。ライセンス契約の条項に同意する場合は、**[I accept the terms of the license agreements]** をクリックし、**[Finish]** をクリックします。(残りの手順は、ライセンス契約の条項に同意したことを前提としています。ライセンス契約の条項に同意しない場合はインストール プロセスを終了します。)
9. インストールを完了するために Eclipse を再起動することを求めるメッセージが表示されたら、**[Restart Now]** をクリックします。

## 関連項目

[Azure Toolkit for Eclipse]

[Azure の Hello World アプリケーションを Eclipse で作成する]

[Azure Toolkit for Eclipse の新機能]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure の Hello World アプリケーションを Eclipse で作成する]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Azure Toolkit for Eclipse の新機能]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_0309_2016-->