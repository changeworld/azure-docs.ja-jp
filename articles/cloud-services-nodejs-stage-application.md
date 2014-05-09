<properties linkid="dev-nodejs-enablestaging" urlDisplayName="展開のステージング" pageTitle="クラウド サービス展開のステージング (Node.js) - Azure" metaKeywords="Azure のステージング, Azure アプリケーションのステージング, Azure のテスト環境, Azure のステージング環境, Azure 仮想 IP スワップ, Azure VIP スワップ" description="Azure アプリケーションをステージング環境に展開してから、仮想 IP (VIP) スワップを使用して運用環境に展開する方法について説明します。" metaCanonical=" " services="cloud-services" documentationCenter="Node.js" title="Azure アプリケーションのステージング" authors="" solutions="" manager="" editor="" />







#Azure アプリケーションのステージング

パッケージ アプリケーションは、インターネット上でアクセスできる
運用環境に移行する前に、Azure でステージング環境に展開して
テストすることができます。ステージング環境は
運用環境とほぼ同じです。唯一の違いは、ステージングされたアプリケーション
には、Azure で生成されたわかりにくい URL を使用しなければ
アクセスできない点です。アプリケーションが正常に動作する
ことを確認したら、仮想 IP (VIP) スワップを実行して、運用環境に
展開できます。

<div class="dev-callout">
	<b>注</b>
	<p>この記事で説明する手順は、Azure クラウド サービスとしてホストされるノード アプリケーションにのみ適用されます。</p>
	</div>

このタスクの手順は次のとおりです。

-   [手順 1. アプリケーションをステージングする]
-   [手順 2. VIP スワップによりアプリケーションを運用環境に展開する]

<h2><a id="step1"></a>手順 1. アプリケーションをステージングする</h2>

このタスクは、**Azure PowerShell** を使用して
アプリケーションをステージングする方法について説明します。

1. サービスを発行するときに、**Publish-AzureServiceProject**
    コマンドレットに **-Slot** パラメーターを渡します。

    **Publish-AzureServiceProject -Slot によるステージング**

2. [Azure 管理ポータル]にログオンし、**[クラウド サービス]** を選択します。クラウド サービスを作成して、**[ステージング]** 列の状態が**実行中**に更新されたら、サービス名をクリックします。

	![実行中のサービスを表示するポータル][cloud-service]

3. **[ダッシュボード]** を選択し、**[ステージング]** を選択します。

	![クラウド サービス ダッシュボード][cloud-service-dashboard]

4. 右側の **[サイトの URL]** エントリの値を書き留めます。DNS 名は、Azure で生成されたわかりにくい内部 ID です。

    ![サイトの URL][cloud-service-staging-url]

これで、ステージング サイトの URL を使用して、アプリケーションがステージング環境で正常に動作していることを確認できます。

ステージングされたアプリケーションが、運用環境に展開済みの
アップグレードされたバージョンであるアップグレード シナリオの場合、
[VIP をスワップすることによって運用環境でアプリケーションを
アップグレード][Step 2: Deploy an Application to Production by Swapping VIPs]します。

<h2><a id="step2"></a>手順 2. VIP スワップにより運用環境でアプリケーションをアップグレードする</h2>

ステージング環境でアップグレードされたバージョンのアプリケーションを
検証したら、ステージング環境と運用環境の仮想 IP (VIP) をスワップする
だけで、そのアプリケーションを運用環境で使用可能にすることができます。

<div class="dev-callout">
<b>注</b>
<p>この手順では、アプリケーションを運用環境に展開済みで、
そのアプリケーションのアップグレードされたバージョンを
ステージング済みであると想定しています。</p>
</div>

1. [Azure 管理ポータル]にログインし、
    **[クラウド サービス]** をクリックして、サービス名を選択します。

2. **[ダッシュボード]** で、**[ステージング]** を選択して、ページの下部にある **[スワップ]** をクリックします。これにより、[VIP のスワップ]
    ダイアログが開きます。

    ![VIP のスワップ ダイアログ][vip-swap-dialog]

3. 情報を確認し、**[OK]** をクリックします。ステージング環境の
    展開が運用環境に切り替わるとき、および運用環境の展開がステージングに
    切り替わるときに、これら 2 つの展開で更新が開始されます。

正常に展開をステージングし、ステージング環境の展開で VIP をスワップ
することによって運用環境の展開をアップグレードしました。

## その他のリソース

- [Azure の VIP スワップによってサービス アップグレードを運用環境に展開する方法]
- [Azure における展開管理の概要]

  [手順 1. アプリケーションをステージングする]: #step1
  [手順 2. VIP スワップによりアプリケーションを運用環境に展開する]: #step2
  [Azure 管理ポータル]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Azure の VIP スワップによってサービス アップグレードを運用環境に展開する方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee517253.aspx
  [Azure における展開管理の概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh386336.aspx

