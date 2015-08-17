<properties 
	pageTitle="クラウド サービス デプロイのステージング (Node.js) - Azure" 
	description="Azure アプリケーションをステージング環境にデプロイしてから、仮想 IP (VIP) スワップを使用して運用環境にデプロイする方法について説明します。" 
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







# Azure アプリケーションのステージング

パッケージ アプリケーションは、インターネット上でアクセスできる運用環境に移行する前に、Azure でステージング環境にデプロイしてテストすることができます。ステージング環境は運用環境とほぼ同じです。唯一の違いは、ステージングされたアプリケーションには、Azure で生成された難読化済みの URL を使用しなければアクセスできない点です。アプリケーションが正常に動作することを確認したら、仮想 IP (VIP) スワップを実行して、運用環境にデプロイできます。

> [AZURE.NOTE]この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。

このタスクの手順は次のとおりです。

-   [手順 1. アプリケーションをステージングする]
-   [手順 2. VIP スワップによりアプリケーションを運用環境にデプロイする]

<h2><a id="step1"></a>手順 1. アプリケーションをステージングする</h2>

このタスクでは、**Microsoft Azure PowerShell** を使用してアプリケーションをステージングする方法を説明します。

1.  サービスを発行するときに、**Publish-AzureServiceProject** コマンドレットに **-Slot** パラメーターを渡します。

    **Publish-AzureServiceProject -Slot staging**

2.  [Azure 管理ポータル]にログオンし、**[クラウド サービス]** を選択します。クラウド サービスを作成して、**[ステージング]** 列の状態が**実行中**に更新されたら、サービス名をクリックします。

	![実行中のサービスを表示するポータル][cloud-service]

3.  **[ダッシュボード]** を選択し、**[ステージング]** を選択します。

	![クラウド サービス ダッシュボード][cloud-service-dashboard]

4. 右側の **[サイトの URL]** エントリの値をメモに記録します。DNS 名は、Azure で生成されたわかりにくい内部 ID です。

    ![サイトの URL][cloud-service-staging-url]

これで、ステージング サイトの URL を使用して、アプリケーションがステージング環境で正常に動作していることを確認できます。

ステージングされたアプリケーションが、運用環境にデプロイ済みのアップグレードされたバージョンであるアップグレード シナリオの場合、[VIP をスワップすることによって運用環境でアプリケーションをアップグレード][Step 2: Deploy an Application to Production by Swapping VIPs]します。

<h2><a id="step2"></a>手順 2. VIP スワップにより運用環境でアプリケーションをアップグレードする</h2>

ステージング環境でアップグレードされたバージョンのアプリケーションを検証したら、ステージング環境と運用環境の仮想 IP (VIP) をスワップするだけで、そのアプリケーションを運用環境で使用可能にすることができます。

> [AZURE.NOTE]この手順では、アプリケーションを運用環境にデプロイ済みで、そのアプリケーションのアップグレードされたバージョンをステージング済みであると想定しています。

1.  [Azure の管理ポータル]にログインし、**[クラウド サービス]** をクリックして、サービス名を選択します。

2.  **[ダッシュボード]** で、**[ステージング]** を選択して、ページの下部にある **[スワップ]** をクリックします。これにより、[VIP のスワップ] ダイアログが開きます。

    ![VIP のスワップ ダイアログ][vip-swap-dialog]

3.  情報を確認し、**[OK]** をクリックします。ステージング環境の 展開が運用環境に切り替わるとき、および運用環境の展開がステージングに 切り替わるときに、これら 2 つの展開で更新が開始されます。

正常にデプロイをステージングし、ステージング環境のデプロイで VIP をスワップすることによって運用環境のデプロイをアップグレードしました。

## その他のリソース

- [Azure の VIP スワップによりサービス アップグレードを運用環境にデプロイする方法]
- [Azure の配置管理の概要]

  [手順 1. アプリケーションをステージングする]: #step1
  [Step 2: Deploy an Application to Production by Swapping VIPs]: #step2
  [手順 2. VIP スワップによりアプリケーションを運用環境にデプロイする]: #step2
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [Azure 管理ポータル]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Azure の VIP スワップによりサービス アップグレードを運用環境にデプロイする方法]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Azure の配置管理の概要]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx
 

<!---HONumber=August15_HO6-->