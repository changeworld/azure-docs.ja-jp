<properties 
	pageTitle="Eclipse で Azure 用の Hello World Web アプリを作成する" 
	description="このチュートリアルでは、Azure Toolkit for Eclipse を使用して、Azure 用の Hello World Web アプリを作成する方法について説明します。" 
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
	ms.date="03/07/2016" 
	ms.author="robmcm"/>

# Eclipse で Azure 用の Hello World Web アプリを作成する

このチュートリアルでは、[Azure Toolkit for Eclipse] を使用して、Web アプリとして基本的な Hello World アプリケーションを作成し、Azure にデプロイする方法について説明します。簡潔にするために JSP の例を紹介していますが、Azure のデプロイに関する限り、基本的な Java サーブレットの場合も手順はほぼ同じです。

このチュートリアルを完了し、作成したアプリケーションを Web ブラウザーで開くと、次の図のようになります。

![][01]
 
## 前提条件

* A Java Developer Kit (JDK) v 1.7 以降。
* Eclipse IDE for Java EE Developers Indigo 以降。<http://www.eclipse.org/downloads/> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、Jetty など)。
* Azure サブスクリプション。<https://azure.microsoft.com/ja-JP/free/> または <http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* Azure Toolkit for Eclipse。詳細については、「[Azure Toolkit for Eclipse のインストール]」を参照してください。

## Hello World アプリケーションを作成するには

最初に、Java プロジェクトを作成します。

1. Eclipse を起動し、メニューから **[File]**、**[New]**、**[Dynamic Web Project]** の順にクリックします(**[File]** と **[New]** のクリック後、使用可能なプロジェクトとして **[Dynamic Web Project]** が表示されない場合は、**[File]**、**[New]**、**[Project]** の順にクリックし、**[Web]** を展開して、**[Dynamic Web Project]**、**[Next]** の順にクリックします)。
1. このチュートリアルでは、プロジェクトに **MyHelloWorld** という名前を付けます画面は次のようになります。![][02]
1. **[完了]** をクリックします。
1. Eclipse の Project Explorer ビューで、**MyHelloWorld** を展開します。**WebContent** を右クリックし、**[New]**、**[JSP File]** の順にクリックします。
1. **[New JSP File]** ダイアログ ボックスで、ファイルに **index.jsp** という名前を付けます。親フォルダーは **MyHelloWorld/WebContent** のまま維持します。
1. **[Select JSP Template]** ダイアログ ボックスで、このチュートリアルのために **[New JSP File (html)]** を選択し、**[Finish]** をクリックします。
1. index.jsp ファイルが Eclipse で開いたら、"**Hello World!**" を動的に表示するためのテキストを既存の `<body>` 要素に追加します。更新された `<body>` コンテンツは、`<body><b><% out.println("Hello World!"); %></b></body>` のようになります。 
1. index.jsp を保存します。

## アプリケーションを Azure Web アプリコンテナーにデプロイするには

Java Web アプリケーションを Azure にデプロイする方法はいくつかあります。このチュートリアルでは、アプリケーションを Azure Web アプリコンテナーにデプロイするという最も簡単な方法について説明します。特殊なプロジェクトの種類や追加のツールは必要ありません。JDK と Web コンテナー ソフトウェアは Azure から提供されるので、自分でアップロードする必要はありません。必要なものは Java Web アプリのみです。結果として、アプリケーションの発行プロセスにかかる時間は分単位ではなく、秒単位になります。

1. Eclipse の Project Explorer で **[MyHelloWorld]** を右クリックします。

1. コンテキスト メニューの **[Azure]**、**[Publish as Azure Web App...]** の順に選択します。![][03]
1. まだ Eclipse から Azure にサインインしていない場合、Azure アカウントにサインインするように求められます。![][04]注: 複数の Azure アカウントがある場合、サインイン プロセス中に同じようなプロンプトが複数回表示されることがあります。このような状況の場合、次のサインイン手順を続行します。
1. Azure アカウントに正常にサインインすると、**[Manage Subscriptions]** ダイアログ ボックスに、資格情報に関連付けられたサブスクリプションの一覧が表示されます。複数のサブスクリプションが表示され、その一部のみを使用する場合、使用するサブスクリプションをオフにすることができます。サブスクリプションを選択したら、**[Close]** をクリックします。![][05]
1. **[Deploy to Azure Web アプリContainer]** ダイアログ ボックスを開くと、以前に作成した Web アプリコンテナーがすべて表示されます。コンテナーを作成していない場合、一覧は空です。![][06]
1. 以前に Azure Web アプリコンテナーを作成していない場合、またはアプリケーションを新しいコンテナーに発行する場合は、次の手順を実行します。作成済みの場合は、既存の Web アプリコンテナーを選択し、以下の手順 7 に進みます。

  1. **[New...]** をクリックします。

  1. **[New Web App Container]** ダイアログ ボックスが表示されます。

        ![][07]

  1. Web アプリコンテナーの **[DNS Label]** を入力します。これで、Azure の Web アプリケーションについて、ホスト URL のリーフ DNS ラベルが構成されます。注: この名は、Azure Web アプリの命名要件に準拠し、使用できる必要があります。

  1. **[Web Container]** ドロップダウン メニューで、アプリケーションに適したソフトウェアを選択します。

        Currently, you can choose from Tomcat 8, Tomcat 7 or Jetty 9. A recent distribution of the selected software will be provided by Azure, and it will run on a recent distribution of JDK 8 created by Oracle and provided by Azure.

  1. **[Subscription]** ドロップダウン メニューで、このデプロイに使用するサブスクリプションを選択します。

  1. **[Resource Group]** ドロップダウン メニューで、Web アプリに関連付けるリソース グループを選択します。

        Note: Azure Resource Groups allow you to group related resources together so that, for example, they can be deleted together.

        You can select an existing Resource Group (if you have any) and skip to step g below, or use the following these steps to create a new Resource Group:

      * **[New...]** をクリックします。

      * **[New Resource Group]** ダイアログ ボックスが表示されます。

            ![][08]

      * **[Name]** テキストボックスに、新しいリソース グループの名前を入力します。

      * **[Region]** ドロップダウン メニューで、リソース グループに適した Azure データ センターの場所を選択します。

      * **[OK]** をクリックします。

  1. **[App Service Plan]** ドロップダウン メニューには、選択したリソース グループに関連付けられた App Service プランが表示されます。

        Note: An App Service Plan specifies information such as the location of your Web App, the pricing tier and the compute instance size. A single App Service Plan can be used for multiple Web Apps, which is why it is maintained separately from a specific Web App deployment.

        You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:

      * **[New...]** をクリックします。

      * **[New App Service Plan]** ダイアログ ボックスが表示されます。

            ![][09]

      * **[Name]** テキストボックスに、新しい App Service プランの名前を入力します。

      * **[Location]** ドロップダウン メニューで、プランに適した Azure データ センターの場所を選択します。

      * **[Pricing Tier]** ドロップダウン メニューで、プランに適した価格を選択します。テスト目的の場合は、**[Free]** を選択できます。

      * **[Instance Size]** ドロップダウン メニューで、プランに適したインスタンス サイズを選択します。テスト目的の場合は、**[Small]** を選択できます。

  1. これらの手順をすべて完了すると、[New Web App Container] ダイアログ ボックスは次の図のようになります。

        ![][10]

  1. **[OK]** をクリックすると、新しい Web アプリコンテナーの作成が完了します。

        Wait a few seconds for the list of the Web App containers to be refreshed, and your newly-created web app container should now be selected in the list.

1. 以上で、初めて Web アプリを Azure にデプロイする処理を完了できます。

    ![][11]

    **[OK]** をクリックして、Java アプリケーションを選択した Web アプリコンテナーにデプロイします。

    注: 既定では、アプリケーションはアプリケーション サーバーのサブディレクトリとしてデプロイされます。ルート アプリケーションとしてデプロイする場合、**[Deploy to root]** チェックボックスをオンにして **[OK]** をクリックします。

1. **[Azure Activity Log]** ビューが開き、Web アプリのデプロイの状態が表示されます。

    ![][12]

    Web アプリを Azure にデプロイするプロセスは、わずか数秒で完了します。アプリケーションの準備ができると、**[Status]** 列に **[Published]** というリンクが表示されます。リンクをクリックすると、デプロイした Web アプリのホーム ページが表示されます。

## Web アプリの更新

既存の実行中の Azure Web アプリを更新するプロセスは短時間で簡単です。更新には 2 つのオプションがあります。

* 既存の Java Web アプリのデプロイを更新できます。
* 同じ Web アプリコンテナーに追加の Java アプリケーションを発行できます。

いずれの場合でもプロセスは同じで、かかる時間は数秒です。

1. Eclipse の Project Explorer で、更新する Java アプリケーションを右クリックするか、既存の Web アプリコンテナーに追加します。

2. コンテキスト メニューが表示されたら、**[Azure]**、**[Publish as Azure Web App...]** の順に選択します。

3. 既にログインしているので、既存の Web アプリコンテナーの一覧が表示されます。Java アプリケーションを発行または再発行するコンテナーを選択し、**[OK]** をクリックします。

数秒後、**[Azure Activity Log]** ビューに更新されたデプロイが **[Published]** と表示され、Web ブラウザーで更新されたアプリケーションを確認できます。

## 既存の Web アプリを停止する

既存の Azure Web アプリコンテナー (とコンテナー内にデプロイされているすべての Java アプリケーション) を停止するには、**Azure Explorer** ビューを使用します。

**Azure Explorer** ビューがまだ開いていない場合、Eclipse の **[Windows]** メニュー、**[Show View]**、**[Other...]**、**[Azure]**、**[Azure Explorer]** の順にクリックします。まだログインしていない場合は、ログインするように求められます。

**Azure Explorer** ビューが表示されたら、次の手順に従って Web App を停止します。

1. **[Azure]** ノードを展開します。
2. **[Web Apps]** ノードを展開します。 
3. 目的の Web App を右クリックします。
5. コンテキスト メニューが表示されたら、**[Stop]** をクリックします。![][13]

## 次のステップ

詳細については、次のリンクを参照してください。

* [Java デベロッパー センター](/develop/java/)
* [Web Apps の概要](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png

<!---HONumber=AcomDC_0309_2016-->