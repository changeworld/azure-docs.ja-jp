<properties linkid="dev-nodejs-enablestaging" urlDisplayName="Staging Deployment" pageTitle="Stage a cloud service deployment (Node.js) - Azure" metaKeywords="Azure staging, Azure application staging, Azure test environment, Azure staging environment, Azure Virtual IP swap, Azure VIP swap" description="Learn how to deploy your Azure application to a staging environment, then deploy to a production environment using Virtual IP (VIP) swap." metaCanonical=" " services="cloud-services" documentationCenter="nodejs" title="Staging an Application in Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Azure アプリケーションのステージング

パッケージ アプリケーションは、インターネット上でアクセスできる運用環境に移行する前に、Azure でステージング環境にデプロイしてテストすることができます。ステージング環境は運用環境とほぼ同じです。唯一の違いは、ステージングされたアプリケーションには、Azure で生成されたわかりにくい URL を使用しなければアクセスできない点です。アプリケーションが正常に動作することを確認したら、仮想 IP (VIP) スワップを実行して、運用環境にデプロイできます。


<b>注</b>
<p>この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。</p>


このタスクの手順は次のとおりです。

-   [手順 1.アプリケーションをステージングする][手順 1.アプリケーションをステージングする]
-   [手順 2.VIP スワップでアプリケーションを運用環境にデプロイする][手順 2.VIP スワップでアプリケーションを運用環境にデプロイする]

## <span id="step1"></span></a>手順 1.アプリケーションをステージングする

このタスクでは、**Microsoft Azure PowerShell** を使用してアプリケーションをステージングする方法を示します。

1.  サービスを発行するときに、 **Publish-AzureServiceProject** コマンドレットに **-Slot** パラメーターを渡します。

    **Publish-AzureServiceProject -Slot によるステージング**

2.  [Azure 管理ポータル][Azure 管理ポータル]にログオンし、**[クラウド サービス]** を選択します。クラウド サービスを作成して、**[ステージング]** 列の状態が**実行中**に更新されたら、サービス名をクリックします。

    ![実行中のサービスを表示するポータル][実行中のサービスを表示するポータル]

3.  **[ダッシュボード]** を選択し、**[ステージング]** を選択します。

    ![クラウド サービス ダッシュボード][クラウド サービス ダッシュボード]

4.  右側の **[サイトの URL]** エントリの値をメモに記録します。DNS 名は、Azure で生成されたわかりにくい内部 ID です。

    ![サイトの URL][サイトの URL]

これで、ステージング サイトの URL を使用して、アプリケーションがステージング環境で正常に動作していることを確認できます。

ステージングされたアプリケーションが、運用環境にデプロイ済みのアプリケーションのアップグレードされたバージョンであるアップグレード シナリオの場合、[VIP スワップによって運用環境でアプリケーションをアップグレード][手順 2.VIP スワップでアプリケーションを運用環境にデプロイする]できます。

## <span id="step2"></span></a>手順 2.VIP スワップによって運用環境でアプリケーションをアップグレードする

ステージング環境でアップグレードされたバージョンのアプリケーションを検証したら、ステージング環境と運用環境の仮想 IP (VIP) をスワップするだけで、そのアプリケーションを運用環境で使用可能にすることができます。

<div class="dev-callout">
<b>注</b>
<p>この手順では、アプリケーションを運用環境にデプロイ済みで、
そのアプリケーションのアップグレードされたバージョンをステージング済みであると
想定しています。</p>
</div>

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインし、 **[クラウド サービス]** をクリックして、サービス名を選択します。

2.  **[ダッシュボード]** で、**[ステージング]** を選択して、ページの下部にある **[スワップ]** をクリックします。これにより、 [VIP のスワップ] ダイアログが開きます。

    ![VIP のスワップ ダイアログ][VIP のスワップ ダイアログ]

3.  情報を確認し、**[OK]** をクリックします。ステージング環境のデプロイメントが運用環境に切り替わるとき、および運用環境のデプロイメントがステージングに切り替わるときに、これら 2 つのデプロイメントで更新が開始されます。

正常にデプロイメントをステージングし、ステージング環境のデプロイメントで VIP をスワップすることによって運用環境のデプロイメントをアップグレードしました。

## その他のリソース

-   [Azure の VIP スワップによってサービス アップグレードを運用環境に展開する方法][Azure の VIP スワップによってサービス アップグレードを運用環境に展開する方法]
-   [Azure における展開管理の概要][Azure における展開管理の概要]

  [手順 1.アプリケーションをステージングする]: #step1
  [手順 2.VIP スワップでアプリケーションを運用環境にデプロイする]: #step2
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [実行中のサービスを表示するポータル]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
  [クラウド サービス ダッシュボード]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [サイトの URL]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [VIP のスワップ ダイアログ]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Azure の VIP スワップによってサービス アップグレードを運用環境に展開する方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee517253.aspx
  [Azure における展開管理の概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh386336.aspx
