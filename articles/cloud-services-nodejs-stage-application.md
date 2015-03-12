<properties 
	pageTitle="クラウド サービス デプロイのステージング (Node.js) - Azure" 
	description="Azure アプリケーションをステージング環境にデプロイしてから、仮想 IP (VIP) スワップを使用して運用環境にデプロイする方法について説明します。" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Azure アプリケーションのステージング

パッケージ アプリケーションは、
インターネット上でアクセスできる運用環境に移行する前に、
Azure でステージング環境にデプロイしてテストすることができます。ステージング環境は
運用環境とほぼ同じです。唯一の違いは、
ステージングされたアプリケーションには、Azure で生成されたわかりにくい URL を使用しなければアクセスできない点です
。アプリケーションが正常に動作することを確認したら、
仮想 IP (VIP) スワップを実行して、
運用環境にデプロイできます。

> [AZURE.NOTE] この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。

このタスクの手順は次のとおりです。

-   [手順 1:アプリケーションをステージングする]
-   [手順 2:VIP スワップでアプリケーションを運用環境にデプロイする]

<h2><a id="step1"></a>手順 1:アプリケーションをステージングする</h2>

このタスクでは、**Microsoft
Azure PowerShell** を使用してアプリケーションをステージングする方法を示します。

1.  サービスを発行するときに、**-Slot** パラメーターを
    **Publish-AzureServiceProject** コマンドレットに渡します。

    **Publish-AzureServiceProject -Slot staging**

2.  [Azure 管理ポータル]にログオンし、**[クラウド サービス]** を選択します。クラウド サービスを作成して、**[ステージング]** 列の状態が**実行中**に更新されたら、サービス名をクリックします。

	![portal displaying a running service][cloud-service]

3.  **[ダッシュボード]** を選択し、**[ステージング]** を選択します。

	![cloud service dashboard][cloud-service-dashboard]

4. 右側の **[サイトの URL]** エントリの値を書き留めます。DNS 名は、Azure で生成されたわかりにくい内部 ID です。

    ![site url][cloud-service-staging-url]

これで、ステージング サイトの URL を使用して、アプリケーションがステージング環境で正常に動作していることを確認できます。

ステージングされたアプリケーションが、
運用環境にデプロイ済みのアプリケーションのアップグレードされたバージョンであるアップグレード シナリオの場合、
[VIP スワップによって運用環境でアプリケーションをアップグレード
][手順 2:VIP スワップでアプリケーションを運用環境にデプロイする]できます。

<h2><a id="step2"></a>手順 2:VIP スワップによって運用環境でアプリケーションをアップグレードする</h2>

ステージング環境でアップグレードされたバージョンのアプリケーションを検証したら、
ステージング環境と運用環境の仮想 IP (VIP) をスワップするだけで、
そのアプリケーションを運用環境で使用可能にすることができます
。

> [AZURE.NOTE] この手順では、アプリケーションを運用環境にデプロイ済みで、
そのアプリケーションのアップグレードされたバージョンをステージング済みであると想定しています
。

1.  [Azure 管理ポータル]にログインし、
    **[クラウド サービス]** をクリックして、サービス名を選択します。

2.  **[ダッシュボード]** で、**[ステージング]** を選択して、ページの下部にある **[スワップ]** をクリックします。これにより、[VIP のスワップ] ダイアログが開きます
    。

    ![vip swap dialog][vip-swap-dialog]

3.  情報を確認し、**[OK]** をクリックします。ステージング環境の
    デプロイが運用環境に切り替わるとき、および運用環境のデプロイがステージングに切り替わるときに、
    これら 2 つのデプロイで更新が開始されます。

正常にデプロイメントをステージングし、
ステージング環境のデプロイで VIP をスワップすることによって運用環境のデプロイをアップグレードしました。

## その他のリソース

- [Azure の VIP スワップによってサービス アップグレードを運用環境にデプロイする方法]
- [Azure の配置管理の概要]

  [手順 1:アプリケーションをステージングする]: #step1
  [手順 2:VIP スワップでアプリケーションを運用環境にデプロイする]: #step2
  [Azure 管理ポータル]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Azure の VIP スワップによってサービス アップグレードを運用環境にデプロイする方法]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Azure の配置管理の概要]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
