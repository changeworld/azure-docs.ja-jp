<properties linkid="dev-nodejs-getting-started" urlDisplayName="Cloud Service" pageTitle="Node.js Getting Started Guide - Azure Tutorial" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="An end-to-end tutorial that helps you develop a simple Node.js web application and deploy it to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ

このガイドでは、Azure のクラウド サービスで実行される簡単な Node.js アプリケーションを作成します。クラウド サービスは、Azure のスケーラブルなクラウド アプリケーションの構成要素です。クラウド サービスによって、アプリケーションのフロントエンド コンポーネントとバックエンド コンポーネントの分離および独立した管理とスケールアウトを実現できます。クラウド サービスは、各ロールを信頼性の高い方法でホストするための堅牢な専用仮想マシンを提供します。

クラウド サービスの詳細と Azure の Web サイトおよび仮想マシンとの違いについては、[「Azure の Web サイト、クラウド サービス、および仮想マシンの比較」][「Azure の Web サイト、クラウド サービス、および仮想マシンの比較」]を参照してください。

<div class="dev-callout"><strong>単純な Web サイトを構築する場合</strong>
<p>実現するシナリオが単純な Web サイトのフロントエンドにのみ関係している場合は、<a href="/ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/">軽量の Azure の Web サイトを使用する</a>ことを検討してください。Web サイトの規模が増大し、要件が変化したときには、容易にクラウド サービスにアップグレードできます。</p>
</div>

このチュートリアルでは、Web ロールでホストされる単純な Web アプリケーションを作成します。コンピューティング エミュレーターを使用してアプリケーションをローカルでテストし、PowerShell コマンド ライン ツールを使用してアプリケーションをデプロイします。

完成したアプリケーションのスクリーンショットは次のようになります。

![hello world ページを表示しているブラウザー ウィンドウこの URL は、ページが Azure でホストされていることを示します。][hello world ページを表示しているブラウザー ウィンドウこの URL は、ページが Azure でホストされていることを示します。]

## 新しいノード アプリケーションの作成

新しい Azure クラウド サービス プロジェクトおよび基本的な Node.js スキャフォールディングを作成するには、次のタスクを実行します。

1.  **[スタート] メニュー**または**スタート画面**で、「**Azure PowerShell**」を検索します。最後に、**[Azure PowerShell]** を右クリックし、**[管理者として実行]** を選択します。

    ![Azure PowerShell アイコン][Azure PowerShell アイコン]

    [WACOM.INCLUDE [install-dev-tools][install-dev-tools]]

2.  C ドライブに新しい **node** ディレクトリを作成し、c:\\node ディレクトリに移動します。

    !["mkdir c:\\node" および "cd node" コマンドが表示されたコマンド プロンプト][]

3.  次のコマンドレットを入力し、新しいソリューションを作成します。

        PS C:\node> New-AzureServiceProject helloworld

        You will see the following response:

    ![New-AzureService helloworld コマンドの結果][New-AzureService helloworld コマンドの結果]

    **New-AzureServiceProject** コマンドレットは、クラウド サービスに発行される新しい Azure ノード アプリケーションを作成するための基本的な構造を生成します。これには、Azure への発行に必要な構成ファイルが含まれています。また、このコマンドレットにより、作業ディレクトリがこのサービス用のディレクトリに変更されます。

    **New-AzureServiceProject** コマンドレットによって作成されるファイルは、次のとおりです。

    -   **ServiceConfiguration.Cloud.cscfg**、
        **ServiceConfiguration.Local.cscfg**、および
        **ServiceDefinition.csdef** は、アプリケーションの発行に必要な、Azure 固有のファイルです。

    これらのファイルの詳細については、
    「[Overview of Creating a Hosted Service for Windows (Azure 対応のホステッド サービスの作成の概要)][Overview of Creating a Hosted Service for Windows (Azure 対応のホステッド サービスの作成の概要)]」を参照してください。

    -   **deploymentSettings.json** には、Azure PowerShell デプロイ コマンドレットによって使用されるローカル設定が格納されます。

4.  次のコマンドを入力して、新しい Web ロールを
    追加します。**Add-AzureNodeWebRole コマンドレット**を使用します。

        PS C:\node\helloworld> Add-AzureNodeWebRole

    次の応答が表示されます。

    ![Add-AzureNodeWebRole コマンドの出力][Add-AzureNodeWebRole コマンドの出力]

    **Add-AzureNodeWebRole** コマンドレットにより、アプリケーション用の新しいディレクトリが作成され、基本的な Node.js アプリケーションのスキャフォールディングが生成されます。前の手順で作成した **ServiceConfiguration.Cloud.csfg**、**ServiceConfiguration.Local.csfg**、および **ServiceDefinition.csdef** ファイルも変更され、新しいロールの構成エントリが追加されます。

    <div class="dev-callout">
<b>注</b>
<p>既定では、ロール名を指定しない場合、ロールが作成されます。名前は、<b>Add-AzureNodeWebRole</b> の最初のパラメーターとして指定できます。たとえば、<code data-inline="1">Add-AzureNodeWebRole MyRole</code> のように指定します。</p>
</div>

5.  次のコマンドを使用して、**WebRole1** ディレクトリに移動し、**server.js** ファイルをメモ帳で開きます。

    PS C:\\node\\helloworld\> cd WebRole1
     PS C:\\node\\helloworld\\WebRole1\> notepad server.js

    この **server.js** ファイルは **Add-AzureNodeWebRole** コマンドレットで作成されたもので、次のスターター コードが含まれています。このコードは、[nodejs.org][nodejs.org] Web サイトにある "Hello World" サンプルに似ています。相違点は次のとおりです。

    -   アプリケーションが、クラウド環境によって割り当てられた
        適切なポートを見つけることができるように、ポートが変更されています。
    -   コンソール ログが削除されています。

    ![メモ帳による server.js の内容の表示][メモ帳による server.js の内容の表示]

## エミュレーターでのアプリケーションのローカル実行

Azure SDK によってインストールされるツールの 1 つに、Azure コンピューティング エミュレーターがあります。このツールを使用すると、アプリケーションをローカルで実行できます。コンピューティング エミュレーターでは、クラウドにデプロイされたときのアプリケーションの実行環境がシミュレートされます。エミュレーターでアプリケーションをテストするには、次のステップを実行します。

1.  メモ帳を閉じ、Windows PowerShell ウィンドウに切り替えます。
    次のコマンドレットを入力してエミュレーターでサービスを実行します。

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

    **–launch** パラメーターを指定すると、エミュレーターでのアプリケーションの実行後、ツールによってブラウザー ウィンドウが自動的に開かれ、アプリケーションが表示されます。以下のスクリーンショットに示すように、ブラウザーが開いて "Hello World" という文字列が表示されます。これは、サービスがコンピューティング エミュレーターで実行され、正常に動作していることを表しています。

    ![Hello World Web ページを表示している Web ブラウザー][Hello World Web ページを表示している Web ブラウザー]

2.  コンピューティング エミュレーターを停止するには、**Stop-AzureEmulator** コマンドを使用します。

    PS C:\\node\\helloworld\\WebRole1\> Stop-AzureEmulator

## Azure へのアプリケーションの展開

    [WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### <span id="download_publishing_settings"></span></a>Azure 発行設定のダウンロード

アプリケーションを Azure に展開するには、まず Azure サブスクリプションの発行設定をダウンロードする必要があります。次のステップでは、このプロセスを説明します。

1.  Windows PowerShell ウィンドウから次のコマンドレットを実行して、ダウンロード ページを起動します。

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

    ブラウザーで、発行設定のダウンロード ページが表示されます。Microsoft アカウントによるログインを求められる場合があります。その場合は、Azure サブスクリプションに関連付けられたアカウントを使用します。

    ダウンロードしたプロファイルを、簡単にアクセスできるファイルの保存場所に保存します。

2.  Azure PowerShell ウィンドウで次のコマンドレットを使用して、ダウンロードした Azure 発行プロファイルを使用するように Windows PowerShell for Node.js コマンドレットを構成します。

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]

    <div class="dev-callout">
<b>注</b>
<p>発行設定をインポートした後、ダウンロードした .publishSettings ファイルには他のユーザーがアカウントにアクセスするために使用できる情報が含まれているので、削除することを検討してください。</p>
</div>

### アプリケーションの発行

1.  以下に示すとおり、**Publish-AzureServiceProject** コマンドレットを
    使用してアプリケーションを発行します。

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

    -   **servicename** パラメーターで、この展開に使用する名前を指定します。必ず一意の名前を使用してください。一意でない場合は発行が失敗します。

    -   **location** パラメーターは、アプリケーションがホストされるデータセンターを指定します。利用可能なデータセンターの一覧を表示するには、**Get-AzureLocation** コマンドレットを使用します。

    -   **launch** パラメーターは、展開の完了後に、ブラウザーの起動とホステッド サービスへの移動を行います。

    発行が成功すると、次のような応答が表示されます。

    ![Publish-AzureService コマンドの出力][Publish-AzureService コマンドの出力]

    **Publish-AzureServiceProject** コマンドレットによって行われる処理は次のとおりです。

2.  Azure に展開されるパッケージを作成します。このパッケージには、node.js アプリケーション フォルダー内のすべてのファイルが含まれます。

3.  **ストレージ アカウント**が存在しない場合は、新たに作成します。Azure ストレージ アカウントは、展開中にアプリケーション パッケージを格納するために使用されます。展開が完了したら、ストレージ アカウントを削除してかまいません。

4.  **クラウド サービス**が存在しない場合は、新たに作成します。**クラウド サービス**は、Azure に展開したアプリケーションがホストされるコンテナーです。詳細については、「[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)][Overview of Creating a Hosted Service for Windows (Azure 対応のホステッド サービスの作成の概要)]」を参照してください。

5.  展開パッケージを Azure に発行します。

    > [WACOM.NOTE]
    > 初めてアプリケーションを発行する場合、アプリケーションが展開され、利用可能になるまでに、5 ～ 7 分かかる場合があります。

    展開が完了すると、ブラウザー ウィンドウが開き、クラウド サービスに移動します。

    ![hello world ページを表示しているブラウザー ウィンドウこの URL は、ページが Azure でホストされていることを示します。][1]

    これで、アプリケーションが Azure 上で実行されました。

## アプリケーションの停止と削除

アプリケーションを展開した後、余分なコストを回避するためにアプリケーションを無効にすることができます。Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。

1.  Windows PowerShell ウィンドウで次のコマンドレットを実行し、前のセクションで作成したサービスの展開を停止します。

        PS C:\node\helloworld\WebRole1> Stop-AzureService

    サービスの停止には、数分間かかる場合があります。サービスが停止すると、停止したことを知らせるメッセージが表示されます。

    ![Stop-AzureService コマンドの状態][Stop-AzureService コマンドの状態]

2.  サービスを削除するには、次のコマンドレットを呼び出します。

        PS C:\node\helloworld\WebRole1> Remove-AzureService

    確認を求めるメッセージが表示されたら、「**Y**」と入力して、サービスを削除します。

    サービスの削除には、数分間かかる場合があります。サービスが削除されると、削除されたことを知らせるメッセージが表示されます。

    ![Remove-AzureService コマンドの状態][Remove-AzureService コマンドの状態]

    <div class="dev-callout">
<strong>注</strong>
<p>サービスを削除しても、サービスが最初に発行されたときに作成されたストレージ アカウントは削除されず、使用したストレージに対して引き続き課金されます。ストレージ アカウントの削除の詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/hh531562.aspx">Azure サブスクリプションからストレージ アカウントを削除する方法</a>」を参照してください。
</p>
</div>

  [「Azure の Web サイト、クラウド サービス、および仮想マシンの比較」]: http://azure.microsoft.com/ja-jp/documentation/articles/choose-web-site-cloud-service-vm/
  [軽量の Azure の Web サイトを使用する]: /ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [hello world ページを表示しているブラウザー ウィンドウこの URL は、ページが Azure でホストされていることを示します。]: https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/ja-jp/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png
  [Azure PowerShell アイコン]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
  [install-dev-tools]: ../includes/install-dev-tools.md
  ["mkdir c:\\node" および "cd node" コマンドが表示されたコマンド プロンプト]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
  [New-AzureService helloworld コマンドの結果]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
  [Overview of Creating a Hosted Service for Windows (Azure 対応のホステッド サービスの作成の概要)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj155995.aspx
  [Add-AzureNodeWebRole コマンドの出力]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
  [nodejs.org]: http://nodejs.org/
  [メモ帳による server.js の内容の表示]: ./media/cloud-services-nodejs-develop-deploy-app/node13.png
  [Hello World Web ページを表示している Web ブラウザー]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
  [Publish-AzureService コマンドの出力]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
  [1]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
  [Stop-AzureService コマンドの状態]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
  [Remove-AzureService コマンドの状態]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
  [Azure サブスクリプションからストレージ アカウントを削除する方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh531562.aspx
