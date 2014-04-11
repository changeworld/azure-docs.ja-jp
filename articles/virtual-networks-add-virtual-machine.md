<properties linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="仮想ネットワークへの VM の追加" pageTitle="仮想ネットワークへの仮想マシンの追加 - Windows Azure" metaKeywords="" description="ストレージ アカウントおよび Windows Azure の仮想ネットワークに追加する仮想マシン (VM) を作成する手順について説明するチュートリアルです。" metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="仮想ネットワークへの仮想マシンの追加" authors=""  solutions="" writer="" manager="" editor=""  />





<h1 id="vnet3">仮想ネットワークへの仮想マシンの追加</h1>

<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

このチュートリアルでは、Windows Azure のストレージ アカウントおよび[仮想ネットワーク](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx)に追加する仮想マシン (VM) を作成する手順について詳しく説明します。

このチュートリアルは、Windows Azure を使用した経験がない読者を対象に作成されています。

<div class="dev-callout"> 
<b>重要</b>

<p>新しい Active Directory フォレストをインストールするために VM を作成する場合は、「<a href="../active-directory-forest/">Windows Azure での新しい Active Directory フォレストのインストール</a>」の手順に従ってください。</p>
</div>


## 目標##

このチュートリアルでは、次の事項について説明します。

-  <a href="#CreateStorageAcct">ストレージ アカウントの作成方法</a>

-  <a href="#CreateVM">仮想マシンを作成して仮想ネットワークに展開する方法</a>

## 前提条件##

-  次のチュートリアルのいずれかを完了します。

	-  [Windows Azure での仮想ネットワークの作成](/ja-jp/manage/services/networking/create-a-virtual-network/)

		-または-
	-  [クロスプレミス接続用の仮想ネットワークの作成](/ja-jp/manage/services/networking/cross-premises-connectivity/)

-  少なくとも 1 つのサブスクリプションが有効でアクティブな Windows Live アカウント。	

-  [「Windows Azure での仮想ネットワークの作成](/ja-jp/manage/services/networking/create-a-virtual-network/)」または「[クロスプレミス接続用の仮想ネットワークの作成](/ja-jp/manage/services/networking/cross-premises-connectivity/)」で設定した次の名前。

	-	仮想ネットワークに割り当てたアフィニティ グループ

	-	仮想ネットワークの名前

	-   サブネットの名前

## <a name="CreateStorageAcct">ストレージ アカウントの作成</a> ##

1.	[Windows Azure の管理ポータル](http://manage.windowsazure.com/)で仮想ネットワークを作成した後、画面の左下隅にある **[新規]** をクリックします。

	![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.	ナビゲーション ウィンドウで、**[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.	次の情報を入力し、画面の右下にあるチェック マークをクリックします。

-  **[URL]:** *自分のストレージ アカウント名*を入力します。

-  **[リージョン/アフィニティ グループ]:** ドロップダウン リストで、**自分のアフィニティ グループ**を選択します。

-  **[Geo レプリケーションの有効化]:** このチェック ボックスはオンのままにしておきます。
 
	![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

4.	処理が完了すると、**[ストレージ]** ページの **[状態]** 列に **[オンライン]** と表示されます。
 
	![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)


## <a name="CreateVM">仮想マシンを作成して仮想ネットワークに展開</a> ##
**仮想マシンを作成して仮想ネットワークに展開するには**

1.	ストレージ アカウントを作成した後、画面の左下隅にある **[新規]** をクリックします。

	![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)


2.	ナビゲーション ウィンドウで、**[コンピューティング]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。
 
	![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)


3.	**[VM OS の選択]** 画面で、**[Windows Server 2008 R2 SP1, October 2012]** (または利用できる最新のバージョン) を選択し、次へ進む矢印をクリックします。
 
	![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)


4.	**[仮想マシンの構成]** 画面で、次の情報を入力して、次へ進む矢印をクリックします。
	<!-- SHOULD WE TELL USERS TO WRITE DOWN USER NAME AND PASS?? -->

	**ヒント:** 新しい仮想マシンにログインするときに使用する資格情報であるため、ユーザー名とパスワードは書き留めてください。

-  **[仮想マシン名]: ** *自分の仮想マシン名*を入力します。

-  **[新しいユーザー名]:** 読み取り専用です。

-  **[新しいパスワード]:** 強力なパスワードを入力します。

-  **[パスワードの確認]:** パスワードを再入力します。

-  **[サイズ]:** **[S]** を選択します。
 
	![VMConfig](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

5.	**[仮想マシン モード]** 画面で、次の情報を入力して、次へ進む矢印をクリックします。

-  **[スタンドアロンの仮想マシン]:** このオプションは選択したままにしておきます。

-  **[DNS 名]: ** *自分のクラウド アプリケーションの DNS 名*を入力します。

-  **[ストレージ アカウント]: ** **自分のストレージ アカウント**を選択します。

-  **[リージョン/アフィニティ グループ/仮想ネットワーク]:** ドロップダウン リストで、**自分の仮想ネットワーク**を選択します。
 
	![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

6.	**[仮想マシンのオプション]** 画面で、次の情報を入力して、チェック マーク ボタンをクリックします。これで仮想マシンが作成されます。新しいマシンが作成されるまで最大 10 分かかることがあります。
	<!-- CONFIRM HOW LONG IT CAN TAKE ON AVG FOR VMACHINE TO BE CREATED -->

-  **[可用性セット]:** **[なし]** を選択します。

-  **[仮想ネットワーク サブネット]:** **[FrontEndSubnet]** を選択します。
	
	<div class="dev-callout"> 
	<b>注</b>

	<p>少なくとも 1 つサブネットを選択する必要があります。ゲートウェイ サブネットは選択しないでください。</p>
	</div> 
 
	![VMOptions](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

7.	仮想マシンを作成すると、仮想マシン画面の **[状態]** 列に **[実行中]** と表示されます。
 
	![VMInstances](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)


8.	ナビゲーション ウィンドウで **[すべてのアイテム]** をクリックします。作成したオブジェクトがすべて現在の状態と共に表示されます。
 
	![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## 次のステップ ##
作成したばかりの VM の内部設置型 Active Directory ドメインに追加のドメイン コントローラーをインストールするには、「[Windows Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](/ja-jp/manage/services/networking/replica-domain-controller/)」を参照してください。

## 関連項目

-  [Windows Azure の仮想ネットワーク](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx)

-  [ネットワーク構成ファイルを使用した仮想ネットワークの構成](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156097.aspx)

<!-- LINKS -->

[wa_com]: http://manage.windowsazure.com/
[Tut2_VN]: ..Tutorial2_CreateVNetCrossPrem 
[Tut1_VN]: ..Tutorial1_CreateVirtualNetwork
























