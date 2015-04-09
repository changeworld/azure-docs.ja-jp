<properties 
	pageTitle="チュートリアル:内部設置型 VMWare サイト間の保護の設定" 
	description="Azure Site Recovery の InMage は、内部設置型 VMWare サイト間でのレプリケーション、フェールオーバー、および回復を実現します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# チュートリアル:内部設置型 VMWare サイト間の保護の設定


<h2><a id="overview" name="overview" href="#overview"></a>概要</h2>

<p>Azure Site Recovery の InMage は、内部設置型 VMWare サイト間のリアルタイムのレプリケーションを実現します。InMage は、Azure Site Recovery サービスのサブスクリプションに含まれます。</p>




<h2><a id="before" name="before" href="#before"></a>前提条件</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Azure アカウント</b> - Azure アカウントが必要です。お持ちでない場合は、<a href="http://aka.ms/try-azure">Azure の無料評価版</a>に関するページを参照してください。サブスクリプションの料金については、<a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery Manager の料金</a>に関するページを参照してください。</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>チュートリアルの手順</h2>
<a name="vault"></a> <h3>手順 1:コンテナーの作成と InMage のダウンロード</h3>

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。


2. <b>[データ サービス]</b> をクリックし、<b>[復旧サービス]</b> を展開して、<b>[Site Recovery コンテナー]</b> をクリックします。


3. <b>[新規作成]</b>、<b>[簡易作成]</b> の順にクリックします。
	
4. <b>[名前]</b> ボックスに、コンテナーを識別する表示名を入力します。

5. <b>[リージョン]</b> ボックスで、コンテナーのリージョンを選択します。サポートされているリージョンについては、<a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery の料金</a>に関するページで利用可能なリージョンを確認してください。

6. <b>[コンテナーの作成]</b> をクリックします。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メインの [復旧サービス] ページで、コンテナーは <b>[アクティブ]</b> と表示されています。</P>

<a name="upload"></a> <h3>手順 2:コンテナーの構成</h3>


1. <b>[復旧サービス]</b> ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. ドロップダウン リストで、**[2 つの内部設置型 VMWare サイト間]** を選択します。
3. InMage Scout をダウンロードします。
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. 製品と一緒にダウンロードされる InMage のドキュメントを使用して、2 つの VMWare サイト間のレプリケーションを設定します。


<h2><a id="next" name="next" href="#next"></a>次のステップ</h2>
<UL>

<LI>疑問がある場合は、<a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services フォーラム</a>にアクセスしてください。</LI> 
</UL>

<!--HONumber=49-->