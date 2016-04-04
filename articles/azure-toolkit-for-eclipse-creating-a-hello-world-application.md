<properties
    pageTitle="Azure 向け Hello World アプリケーションを Eclipse で作成する"
    description="Azure Toolkit for Eclipse を使用してシンプルな Hello World アプリケーションを作成する方法について説明します。"
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
    ms.date="03/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Azure 向け Hello World アプリケーションを Eclipse で作成する #

以降の手順では、Azure Toolkit for Eclipse を使って基本的な JSP アプリケーションを作成し Azure にデプロイする方法を説明しています。簡潔にするために JSP の例を紹介していますが、Azure のデプロイに関する限り、Java サーブレットの場合も手順はほぼ同じです。

ここでは次のようなアプリケーションを作成します。

![][ic600360]

## 前提条件 ##

* A Java Developer Kit (JDK) v 1.7 以降。
* Eclipse IDE for Java EE Developers Indigo 以降。<http://www.eclipse.org/downloads/> からダウンロードできます。
* Java ベースの Web サーバーまたはアプリケーション サーバーのディストリビューション (Apache Tomcat、GlassFish、JBoss Application Server、Jetty、IBM® WebSphere® Application Server Liberty Core など)。
* Azure サブスクリプション。<http://azure.microsoft.com/pricing/purchase-options/> から入手できます。
* Azure Toolkit for Eclipse。詳細については、「[Azure Toolkit for Eclipse のインストール][]」を参照してください。

## Hello World アプリケーションを作成するには ##

最初に、Java プロジェクトを作成します。

*  Eclipse を起動し、メニューから **[File]**、**[New]**、**[Dynamic Web Project]** の順にクリックします(**[File]** と **[New]** のクリック後、使用可能なプロジェクトとして **[Dynamic Web Project]** が表示されない場合は、**[File]**、**[New]**、**[Project]** の順にクリックし、**[Web]** を展開して、**[Dynamic Web Project]**、**[Next]** の順にクリックします)。
*  このチュートリアルでは、プロジェクトに **MyHelloWorld** という名前を付けます(この名前を必ず使用してください。以降の手順は、WAR ファイルの名前が MyHelloWorld であることを前提にしています)。画面は次のようになります。
    ![][ic589576]
* **[完了]** をクリックします。
* Eclipse の Project Explorer ビューで、**MyHelloWorld** を展開します。**WebContent** を右クリックし、**[New]**、**[JSP File]** の順にクリックします。
* **[New JSP File]** ダイアログ ボックスで、ファイルに **index.jsp** という名前を付けます。次に示しているように、親フォルダーは引き続き **MyHelloWorld/WebContent** としてください。
    ![][ic659262]
* **[Select JSP Template]** ダイアログで、このチュートリアルのために **[New JSP File (html)]** を選択し、**[Finish]** をクリックします。
* index.jsp ファイルが Eclipse で開いたら、"**Hello World!**" を動的に表示するためのテキストを既存の `<body>` 要素に追加します。更新した `<body>` の内容は次のようになります。
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* index.jsp を保存します。

## アプリケーションをすばやく簡単に Azure にデプロイするには ##

Java Web アプリケーションをテストする準備が整ったらすぐに、以下に示す簡単な手順に従い、Azure クラウド上で直接アプリケーションをテストすることができます。

1. Eclipse の Project Explorer で **[MyHelloWorld]** をクリックします。
1. Eclipse ツール バーの **[Publish to Azure Cloud]** ボタン をクリックします。
    ![][ic710882]
1. このアプリケーションを Azure に発行するのが初めてで、アプリケーションの Azure デプロイ プロジェクトを過去に作成したことがない場合、Azure デプロイ プロジェクトが自動的に作成されます。次のプロンプトが表示され、アプリケーションを実行する目的で自動的にデプロイされる JDK パッケージとアプリケーション サーバーが表示されます。
    ![][ic789598]
	この方法を使えば、Azure ですばやく簡単にアプリケーションをテストすることができます。サーバーを個別に構成したり、既定とは異なる JDK を構成したりする必要はありません。既定の設定で問題がない場合は、**[OK]** をクリックして以降の手順に進みます。
	ただし、アプリケーションに使用する JDK またはアプリケーション サーバーを変更する必要がある場合は、自動的に作成された Azure デプロイ プロジェクトを編集して後で変更することができます。または、ここで **[Cancel]** をクリックし、このチュートリアルの「**Azure デプロイ プロジェクトについて**」をお読みください。
1. **[Publish to Azure]** ダイアログで次の操作を行います。
    1. 選択するサブスクリプションが **[Subscription]** ボックスの一覧にまだ存在しない場合は、次の手順に従って該当するサブスクリプション情報をインポートしてください。
        1. **[Import from PUBLISH-SETTINGS file]** をクリックします。
        1. **[Import Subscription Information]** ダイアログの **[Download PUBLISH-SETTINGS File]** をクリックします。まだ Azure アカウントにログインしていない場合は、ログインするように求められます。その後、Azure 発行設定ファイルを保存するよう求めるメッセージが表示されます。このファイルは、ローカル コンピューターに保存してください。
        1. 引き続き **[Import Subscription Information]** ダイアログで **[Browse]** ボタンをクリックし、前の手順でローカルに保存した発行設定ファイルを選択して **[Open]** をクリックします。画面は次のようになります。
		![][ic644267]
        1. **[OK]** をクリックします。
    1. デプロイに使用するサブスクリプションを **[Subscription]** で選択します。
    1. 使用するストレージ アカウントを **[Storage account]** で選択するか、**[New]** をクリックして新しいストレージ アカウントを作成します。
    1. 使用するクラウド サービスを **[Service name]** で選択するか、**[New]** をクリックして新しいクラウド サービスをクリックします。
    1. デプロイで使用するオペレーティング システムのバージョンを **[Target OS]** で選択します。
    1. このチュートリアルの目的上、**[Target environment]** では **[Staging]** を選択してください(運用環境のサイトにデプロイする準備が整った段階で **[Production]** に変更します)。
    1. (省略可) 以前のデプロイを新しいデプロイで自動的に上書きする場合は、**[Overwrite previous deployment]** チェック ボックスをオンにしてください。このオプションを有効にしておくと、同じ場所に発行したときに "409 競合" の問題が発生しなくなります。**[Publish to Azure]** ダイアログに **[Remote Access]** のセクションが存在することに注目してください。既定ではリモート アクセスが無効になっており、この例では有効にしません。リモート アクセスを有効にする場合は、リモートからログインする際に使用するユーザー名とパスワードを入力します。リモート アクセスの詳細については、[Eclipse で Azure デプロイのリモート アクセスを有効にする方法][]に関するページを参照してください。実際の **[Publish to Azure]** ダイアログの例を次に示します。 
    	![][ic719488]
1. **[Publish]** をクリックしてステージング環境に発行します。フル ビルドの実行を求められたら、**[Yes]** をクリックします。初回ビルドには数分かかる場合があります。Eclipse のタブ表示セクションに **[Azure Activity Log]** が表示されます。![][ic719489] このログと **[Console]** ビューを使用して、デプロイの進行状況を確認できます。また、[Azure 管理ポータル][]にログインし、**[Cloud Services]** セクションを使用して状況を監視することもできます。
1. デプロイが正常に完了すると、**[Azure Activity Log]** のステータスに **[Published]** と表示されます。次の図に示すように、**[Published]** をクリックすると、デプロイのインスタンスがブラウザーに表示されます。 
	![][ic719490]

ステージング環境へのデプロイであるため、DNS 名は http://&lt;*guid*&gt;.cloudapp.net という形式になります。この DNS 名にアプリケーションのサフィックスを付加した文字列が URL に使われます。たとえば、「http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld」のように入力します。**MyHelloWorld** 部分は大文字と小文字が区別されます。 Azure Platform Management Portal (管理ポータルの [Cloud Services] の部分) でデプロイ名をクリックして、DNS 名を確認することもできます。

このチュートリアルは、ステージング環境へのデプロイを想定しましたが、運用環境へのデプロイも手順は変わりません。ただし、**[Publish to Azure]** ダイアログの **[Target environment]** では、**[Staging]** ではなく **[Production]** を選択する必要があります。運用環境にデプロイした場合、ステージング環境へのデプロイで使用された GUID ではなく、選択した DNS 名が URL に使用されます。

>[AZURE.WARNING] この時点で、クラウドに対する Azure アプリケーションのデプロイは完了しています。ただし、先に進む前に注意してください。デプロイされたアプリケーションは実行中以外の時間もサブスクリプションの課金の対象になります。そのため、不要なデプロイを Azure サブスクリプションから削除することがきわめて重要となります。

## Azure デプロイ プロジェクトについて ##

1 つ以上の Java アプリケーションを Azure にデプロイするには、Azure デプロイ プロジェクトが必要です。このプロジェクトは、アプリケーションを Azure に発行するためにラップする必要のある "パッケージ" の役割を果たします。

Azure デプロイ プロジェクトには、アプリケーションの情報に加え、デプロイの重要な構成要素についての情報が格納されます。とりわけ重要なのは、Web アプリを実行するためのアプリケーション サーバー コンテナーとその実行環境となる Java ランタイムです。その選択肢として、Azure は、豊富な Java ランタイムと Java アプリケーション サーバーをサポートしています。

ここでは、わかりやすくするためにごく単純な例を用いていますが、Azure デプロイ プロジェクトには他にも重要な構成情報を含めることができます。実際のアプリケーションではそれらの構成情報を使って、さまざまな要素をほぼ制限なく組み合わせ、スケーラビリティと可用性に優れた多層クラウド サービスを作成することが可能です。**session affinity ("sticky sessions")**、**fast caching**、**remote debugging**、**SSL offloading**、**firewall/port routing**、**remote access** など、強力な機能が豊富に用意されています。

ここで、前のセクション (「アプリケーションをすばやく簡単に Azure にデプロイするには」) の作業が完了していれば、自動的に生成された新しい Azure デプロイ プロジェクトが、"**MyHelloWorld\_onAzure**" という名前で Project Explorer に表示されます。

このチュートリアルと同様の手順に従えば、空の Azure デプロイ プロジェクトを自分で作成し、既存のアプリケーションを追加することもできます。その場合、必要な作業は増えますが、その分、当初から初期構成を細かく制御することができます。

新しい Azure デプロイ プロジェクトをゼロから作成するには、**[New Azure Deployment Project]** ボタン ![][ic710876] をクリックします。

既存の Azure デプロイ プロジェクトを使用しているか、ゼロから作成しているかに関係なく、その構成設定やコンポーネント (JDK やアプリケーション サーバーなど) は、いつでも同じように簡単に変更することができます。

既存の Azure デプロイ プロジェクトで、JDK、アプリケーション サーバー、またはアプリケーションの一覧を変更するには、次の操作を実行します。

1. Project Explorer でプロジェクト ノード (例: **MyHelloWorld\_onAzure**) を展開します。
2. **[WorkerRole1]** を右クリックします。
3. コンテキスト メニューの **[Azure]** サブメニューを展開します。
4. **[Server Configuration]** をクリックします。

ここで紹介した手順に従って既存の Azure デプロイ プロジェクトを編集することによってサーバーを構成した場合も、新しいプロジェクトをゼロから作成してサーバーを構成した場合も、同じ種類のダイアログが表示され、そのダイアログで JDK、サーバー、アプリケーション コンポーネントを構成することができます。JDK やアプリケーション サーバーの変更、デプロイにおけるアプリケーションの追加と削除など、これらのダイアログの設定を変更する方法の詳細については、「[サーバー構成のプロパティ][]」を参照してください。

## Windows のみ: アプリケーションをコンピューティング エミュレーターにデプロイするには ##

>[AZURE.NOTE] Azure エミュレーターは、Windows でのみ利用できます。Windows 以外のオペレーティング システムをご利用の場合、このセクションはスキップしてください。

前述の手順に従いアプリケーションを Azure に発行することによって暗黙的に新しい Azure デプロイ プロジェクトを作成した場合、JDK とアプリケーション サーバーはクラウド用に構成されます。つまり、ローカル エミュレーション用には構成されていません。プロジェクトをローカル エミュレーターでテストするには、次の手順に従います。

1. Eclipse の Project Explorer で **[MyHelloWorld\_onAzure]** をクリックします。
1. **[WorkerRole1]** を右クリックします。
1. コンテキスト メニューの **[Azure]** サブメニューを展開します。
1. **[Server Configuration]** をクリックします。
1. **[JDK]** タブで、事前に構成された既定のローカル JDK がツールキットにあるかどうかを確認します。ない場合、または既定値を変更する場合は、**[Use the JDK from this file path for testing locally]** チェック ボックスをオンにして、使用する JDK のインストール場所を指定します。変更する場合は、**[Browse]** ボタンをクリックし、使用する JDK のディレクトリを参照機能付きコントロールで選択します。
1. [**Server**] タブをクリックします。
1. ダイアログ ボックスの下部にある **[Local server path]** ボックスに、ダイアログ ボックスの上部の **[Deploy a server of this type]** チェック ボックスで選択したサーバーの種類とメジャー バージョン番号と一致する、ローカルにインストールされているサーバーのパスを入力します。異なる種類または異なるメジャー バージョンのアプリケーション サーバーを使う場合は、最初に [Deploy a server of this type] チェック ボックスの選択を変更してください。
1. **[OK]** をクリックします。
1. Eclipse のツール バーで、**[Run in Azure Emulator]** ボタン ![][ic710879] をクリックします。**[Run in Azure Emulator]** ボタンが有効になってない場合は、Eclipse の Project Explorer で **[MyHelloWorld\_onAzure]** が選択されていることと、Eclipse の Project Explorer が現在のウィンドウとしてフォーカスされていることを確認してください。まずプロジェクトのフル ビルドが開始され、その後、コンピューティング エミュレーターで Java Web アプリケーションが起動します(コンピューターの性能特性によっては、最初のビルドに数秒から数分かかる場合がありますが、それ以降のビルドはもっと短時間で完了します)。 最初のビルド ステップの完了後、ご利用のコンピューターへの変更をコマンドに許可するかどうかの確認が、Windows ユーザー アカウント制御 (UAC) から求められます。**[はい]** をクリックします。

>[AZURE.IMPORTANT] UAC プロンプトが表示されない場合はまず、Windows タスク バーに UAC アイコンが表示されているかどうかを確認し、表示されていればクリックしてください。UAC プロンプトが最前面に表示されない場合がありますが、その場合でも、タスク バーにはアイコンとして表示されます。

1. コンピューティング エミュレーターの UI から出力された情報を見て、プロジェクトに問題がないかどうかを確認します。デプロイの内容によっては、コンピューティング エミュレーター内でアプリケーションが完全に起動するまでにしばらく時間がかかる場合があります。
1. ブラウザーを起動し、アドレスとして `http://localhost:8080/MyHelloWorld` という URL を指定します (URL の `MyHelloWorld` 部分は大文字と小文字が区別されます)。次の画像のような MyHelloWorld アプリケーション (index.jsp の出力) が表示されます。
    ![][ic589579]

コンピューティング エミュレーターでの実行を停止するには、Eclipse のツール バーの **[Reset Azure Emulator]** ![][ic710880] をクリックします。

## デプロイを削除するには ##

Azure Toolkit for Eclipse 内でデプロイを削除するには、Eclipse の Project Explorer で **[MyHelloWorld\_onAzure]** が選択されていることと、Project Explorer が現在のウィンドウとしてフォーカスされていることを確認し、Eclipse のツール バーの **[Unpublish]** ボタン ![][ic710883] をクリックします(Eclipse の Project Explorer で **[MyHelloWorld\_onAzure]** を右クリックし、**[Azure]**、**[Undeploy from Azure Cloud]** の順にクリックしても同じ操作を実行できます)。 **[Unpublish Azure Project]** ダイアログが表示されます。

![][ic719491]

対象のデプロイがあるクラウド サービスとサブスクリプションを選択し、削除するデプロイを選択して **[Unpublish]** をクリックします。

ツールキットを使う以外にも、Azure 管理ポータルの **[Cloud Services]** セクションを使ってデプロイを削除することもできます。その場合、対象のデプロイに移動してそのデプロイを選択し、**[削除]** ボタンをクリックします。この場合、デプロイは停止されてから削除されます。デプロイを停止するだけで削除しない場合は、**[削除]** ではなく **[停止]** をクリックしてください。ただし、既に述べたように、デプロイを削除しないと、たとえデプロイを停止していても、引き続きデプロイに関する料金が発生します。

## 関連項目 ##

[Azure Toolkit for Eclipse][]

[Azure Toolkit for Eclipse のインストール][]

[Azure Toolkit for Eclipse の新機能][]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][]を参照してください。

<!-- URL List -->

[Azure Java デベロッパー センター]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure 管理ポータル]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Eclipse で Azure デプロイのリモート アクセスを有効にする方法]: http://go.microsoft.com/fwlink/?LinkID=699538
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546
[サーバー構成のプロパティ]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Azure Toolkit for Eclipse の新機能]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png

<!----HONumber=AcomDC_0309_2016-->