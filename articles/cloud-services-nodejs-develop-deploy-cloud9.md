<properties 
	pageTitle="Cloud9 を使用した Node.js のデプロイ - Azure チュートリアル" 
	description="Cloud9 IDE を使用して Node.js アプリケーションを開発、構築し、Azure にデプロイする方法について説明します。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="mwasson"/>






# Cloud9 からの Azure アプリケーションのデプロイ

このチュートリアルでは、Cloud9 IDE を使用して Node.js アプリケーションを開発してビルドし、Azure にデプロイする方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

-   Cloud9 IDE プロジェクトを作成する
-   Azure にプロジェクトをデプロイする
-   既存の Azure のデプロイメントを更新する
-   ステージング環境のデプロイと運用環境のデプロイの間でプロジェクトを移動する

[Cloud9 IDE][] は、ブラウザー ベースのクロスプラットフォーム開発環境を提供します。Cloud9 でサポートしている Node.js プロジェクト用の機能の 1 つとして、IDE 内から Azure に直接デプロイできます。また、Cloud9 は GitHub および BitBucket リポジトリ サービスとも統合されており、他のユーザーと簡単にプロジェクトを共有できます。

Cloud9 を使用すると、数多くの最新のブラウザーおよびオペレーティング システムからアプリケーションを開発して Azure にデプロイできます。追加の開発ツールや SDK をローカルにインストールする必要はありません。次に示す手順は、Mac で Google Chrome を使用する場合の手順です。

## サインアップ

Cloud9 を使用するには、まず Cloud9 の Web サイトにアクセスして、[サブスクリプションを登録][Cloud9 IDE]する必要があります。既存の GitHub または BitBucket のアカウントを使用してサインインすることも、Cloud9 のアカウントを作成することもできます。無料のサブスクリプションを利用することも、より多くの機能が提供される有料サブスクリプションを利用することもできます。詳細については、[Cloud9 IDE][] を参照してください。

## Node.js プロジェクトの作成

1.  Cloud9 にサインインし、**[My Projects]** の横にある **+** 記号をクリックして、**[Create a new project]** を選択します。

	![新しい Cloud9 プロジェクトの作成](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  **[Create a new project]** ダイアログで、プロジェクト名、アクセス、プロジェクトの種類を入力します。**[Create]** をクリックしてプロジェクトを作成します。

	![新しいプロジェクトの作成ダイアログ Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE]一部のオプションでは、Cloud9 の有料プランが必要です。
	   
	> [AZURE.NOTE]Cloud9 プロジェクトのプロジェクト名は、Azure に展開するときには使用されません。

3.  プロジェクトが作成されたら、**[Start Editing]** をクリックします。Cloud9 IDE を初めて使用する場合は、サービスの説明ツアーを開始するためのオプションが表示されます。ツアーをスキップして後日見る場合は、**[Just the editor, please]** を選択します。

	![Cloud9 プロジェクトの編集の開始](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  新しい Node アプリケーションを作成するには、**[File]**、**[New File]** の順に選択します。

	![Cloud9 プロジェクト内での新しいファイルの作成](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  **Untitled1** というタイトルの新しいタブが表示されます。**[Untitled1]** タブに次のコードを入力して、Node アプリケーションを作成します。

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE]process.env.PORT を使用することによって、アプリケーションを Cloud9 デバッガーで実行するか、Azure に展開するかに関係なく、アプリケーションで適切なポートが選択されます。

6.  コードを保存するには、**[File]**、**[Save]** の順に選択します。**[Save As]** ダイアログで、ファイル名として「**server.js**」と入力し、**[Save]** をクリックします。


	> [AZURE.NOTE]req 変数が使用されていないことを示す警告の記号が表示されます。この警告は無視してかまいません。

	![server.js ファイルの保存](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## アプリケーションの実行

> [AZURE.NOTE]ここで示す手順は、Hello World アプリケーションでは十分ですが、外部モジュールを使用するアプリケーションでは、デバッグ環境として特定のバージョンの Node.js を選択することが必要になる場合があります。そのためには、デバッグのドロップダウンで **[Configure...]** を選択し、Node.js の特定のバージョンを選択します。たとえば、"azure" モジュールを使用しているときに、Node.js 0.6.x が選択されていない場合、認証エラーが表示されることがあります。


1.  **[Debug]** をクリックして、Cloud9 デバッガーでアプリケーションを実行します。
	
	![デバッガーでの実行](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  出力ウィンドウが表示されます。ブラウザー ウィンドウからアプリケーションに アクセスするには、表示される URL をクリックします。

	![出力ウィンドウ](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	作成されたアプリケーションは、次のように表示されます。

	![ブラウザーで実行中のアプリケーション](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  アプリケーションのデバッグを停止するには、**[stop]** をクリックします。

## Azure アカウントの作成

アプリケーションを Azure に展開するには、アカウントが必要です。Azure アカウントを持っていない場合は、次の手順に従って無料評価版アカウントにサインアップすることもできます。

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## デプロイの作成

1.  新しいデプロイメントを作成するには、**[Deploy]** を選択し、**[+]** をクリックしてデプロイ サーバーを作成します。

    ![新しいデプロイの作成][create a new deployment]

2.  **[Add a deploy target]** ダイアログで、デプロイメント名を入力し、**[Choose type]** の一覧で **[Azure]** を選択します。指定した展開名は、Cloud9 内で展開を識別するために使用され、Azure 内の展開名とは対応しません。

3.  初めて Azure を使用する Cloud9 のデプロイメントを作成した場合は、Azure 発行設定を構成する必要があります。これらの設定をダウンロードして Cloud9 にインストールするには、次の手順を実行します。

    1.  **[Download Azure Settings]** をクリックします。

        ![発行設定のダウンロード](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        これによって、Azure 管理ポータルが開き、Azure 発行設定のダウンロードを確認するメッセージが表示されます。作業を開始する前に、Azure アカウントにログインする必要があります。

    2.  発行設定ファイルをローカル ドライブに保存します。

    3.  **[Add a deploy target]** ダイアログで、**[Choose File]** を選択し、前の手順でダウンロードしたファイルを選択します。

    4.  ファイルを選択した後、**[Upload]** をクリックします。

4.  **[+ Create new]** をクリックして、新しいホステッド サービスを作成します。*ホステッド サービス*は、Azure に展開したアプリケーションがホストされるコンテナーです。詳細については、「[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)][]」を参照してください。

	![新しいデプロイの作成](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  新しいホステッド サービスの名前と、インスタンス数、ホスト OS、データ センターなどの構成オプションを指定するよう求めるメッセージが表示されます。指定した展開名は、Azure でホステッド サービス名として使用されます。この名前は Azure システム内で一意である必要があります。
	
	![新しいホステッド サービスの作成](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE]**[Add a deploy target]** ダイアログの **[Choose existing deployment]** には、既存の Azure ホステッド サービスがすべて表示されます。既存のホステッド サービスを選択すると、このプロジェクトはそのサービスに対してデプロイされます。

	> [AZURE.NOTE]**[Enable RDP]** を選択し、ユーザー名とパスワードを指定すると、デプロイメントのリモート デスクトップが有効になります。


## Azure 運用環境へのデプロイ

1.  前の手順で作成したデプロイメントを選択します。ダイアログが 表示され、この展開に関する情報と、Windows Azure への 展開後に使用される運用環境の URL が 示されます。

	![デプロイの選択](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  **[Deploy to Production environment]** を選択します。

3.  **[Deploy]** をクリックしてデプロイを開始します。

4.  初めてこのプロジェクトを Azure にデプロイする場合、**"No web.config found"** というエラーが表示されます。**[Yes]** を選択してファイルを作成します。これにより、"Web.cloud.config" ファイルがプロジェクトに追加されます。
	
	![no web.config file found メッセージ](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  初めてこのプロジェクトを Azure にデプロイする場合、**"No 'csdef' file present"** というエラーが表示されます。**[Yes]** を選択して .csdef ファイルを作成します。これにより、"ServiceDefinition.csdef" ファイルがプロジェクトに追加されます。ServiceDefinition.csdef は、アプリケーションの発行に必要な Azure 固有のファイルです。詳細については、「[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)][]」を参照してください。

6.  このアプリケーションのインスタンスのサイズを選択するよう求めるメッセージが表示されます。**[Small]** を選択し、**[Create]** をクリックします。Azure VM のサイズの詳細については、「[仮想マシンのサイズの構成方法][]」を参照してください。

	![csdef ファイルの値の指定](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  デプロイメントのエントリは、デプロイ プロセスの状態を示します。完了すると、展開は **Active** と表示されます。

	![デプロイの状態](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE]Cloud 9 IDE によって展開されるプロジェクトには、Azure の展開名として GUID が割り当てられます。

8.  デプロイメント ダイアログには、運用環境の URL へのリンクが含まれます。展開が完了したら、URL をクリックして Azure で実行されているアプリケーションを表示します。

	![Azure 運用環境の URL リンク](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

## アプリケーションの更新

アプリケーションを変更するときは、Cloud9 を使用して、更新されたアプリケーションを同じ Azure ホステッド サービスに展開できます。

1.  server.js ファイルで、"hello azure v2" が画面に出力されるようにコードを更新します。既存のコードを次の更新されたコードに置き換えます。

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  コードを保存するには、**[File]**、**[Save]** の順に選択します。

## Azure ステージング環境への更新のデプロイ

1.  **[Deploy to Staging]** を選択します。

2.  **[Deploy]** をクリックしてデプロイを開始します。

	Azure ホステッド サービスはそれぞれステージング環境と運用環境の 2 つの環境をサポートしています。ステージング環境は運用環境とほぼ同じです。唯一の違いは、ステージングされたアプリケーションには、Azure で生成されたわかりにくい GUID ベースの URL を使用しなければアクセスできない点です。ステージング環境を使用してアプリケーションをテストすることができ、変更内容を確認したら、後で説明する仮想 IP (VIP) スワップを実行して、ステージング環境のバージョンを運用環境に移行できます。

3.  アプリケーションがステージング環境にデプロイされると、次のスクリーンショットに示されているように、コンソール出力に GUID ベースのステージング URL が表示されます。この URL をクリックして、ステージングされたアプリケーションをブラウザーで開きます。

	![ステージング URL を示すコンソール出力](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## VIP スワップを使用した運用環境への更新の移行

運用環境またはステージング環境にサービスをデプロイすると、その環境のサービスに、仮想 IP アドレス (VIP) が割り当てられます。サービスをステージング環境から運用環境に移行するには、VIP スワップを実行して、ステージング環境と運用環境のデプロイをスワップすることによって、再デプロイすることなく、この処理を行うことができます。VIP スワップでは、テストされたステージング環境のアプリケーションが運用環境に移行され、運用環境でダウンタイムは発生しません。詳細については、[Azure でのデプロイ管理の概要][]に関するページを参照してください。

1.  デプロイ ダイアログで、**[Open portal]** リンクをクリックして、Azure 管理ポータルを開きます。

	![展開ダイアログから Azure 管理ポータルへのリンク][Link from deploy dialog to Azure Management Portal]

2.  自分の資格情報を使用してポータルにサインインします。

3.  Web ページの左側で、**[ホステッド サービス、ストレージ アカウント、CDN]** を選択し、**[ホステッド サービス]** をクリックします。

	![Azure 管理ポータル][Azure Management Portal]

	結果のウィンドウに、Cloud9 で指定した名前のホステッド サービスと 2 つの展開が表示されます。1 つは **[環境]** の値が **[ステージング]** で、もう 1 つは **[運用]** です。

4.  VIP スワップを実行するには、ホステッド サービスを選択し、リボンの **[VIP のスワップ]** をクリックします。

	![VIP スワップ](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  表示された [VIP スワップ] ダイアログ ボックスで **[OK]** をクリックします。

6.  運用環境のアプリケーションを参照します。以前にステージング環境に展開されたアプリケーションのバージョンが、運用環境にあることがわかります。

	![Azure で実行されている運用環境のアプリケーション](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## リモート デスクトップの使用

デプロイを作成するときに、RDP を有効にし、ユーザー名とパスワードを指定した場合は、特定のインスタンスを選択し、リボンの [接続] を選択することによって、リモート デスクトップを使用してホステッド サービスに接続できます。

![インスタンスへの接続](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

[接続] をクリックすると、.RDP ファイルを開くか、ダウンロードするよう求めるメッセージが表示されます。このファイルには、リモート デスクトップ セッションに接続するために必要な情報が含まれています。このファイルを Windows システムで実行すると、デプロイを作成するときに入力したユーザー名とパスワードの入力を求めるメッセージが表示され、選択したインスタンスのデスクトップに接続されます。

> [AZURE.NOTE]ホストされているアプリケーションのインスタンスに接続するための .RDP ファイルは、Windows 上のリモート デスクトップ アプリケーションについてのみ動作します。

## アプリケーションの停止と削除

Azure では、ロール インスタンスで消費されたサーバー時間の時間単位で課金されます。サーバー時間はアプリケーションが展開されている間、消費されます。インスタンスが実行されていない場合や、停止状態の場合にも消費されます。また、サーバー時間は運用環境とステージング環境の両方のデプロイで消費されます。

Cloud9 は IDE を提供することに重点を置いており、Azure に展開された後のアプリケーションを直接停止または削除する方法は提供していません。Azure でホストされるアプリケーションを削除するには、次の手順を実行します。

1.  デプロイ ダイアログで、**[Open portal]** リンクをクリックして、Azure 管理ポータルを開きます。

	![展開ダイアログから Azure 管理ポータルへのリンク](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  自分の資格情報を使用してポータルにサインインします。

3.  Web ページの左側で、**[ホステッド サービス、ストレージ アカウント、CDN]** を選択し、**[ホステッド サービス]** をクリックします。

4.  ステージング環境のデプロイメント (**[環境]** の値で示される) を選択します。リボンの **[削除]** をクリックしてアプリケーションを削除します。

	![デプロイの削除](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  運用環境のデプロイメントを選択し、**[削除]** をクリックしてこのアプリケーションも削除します。

## その他のリソース

-   [Cloud9 に関するドキュメント][]


[Cloud9 IDE]: http://cloud9ide.com/
[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[仮想マシンのサイズの構成方法]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
[Azure でのデプロイ管理の概要]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
[Cloud9 に関するドキュメント]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=54-->