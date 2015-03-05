<properties 
	pageTitle="Visual Studio を使用して Web プロジェクト用の仮想マシンを作成する" 
	description="Web サイト用の仮想マシンを作成します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Visual Studio で Web サイト用の仮想マシンを作成する

Azure の Web サイト用の Web プロジェクトを作成する際に、Azure 内で仮想マシンをプロビジョニングできます。さらに、他のソフトウェアで仮想マシンを構成したり、診断やデバッグのために仮想マシンを使用したりすることができます。

Web サイトを作成する際に仮想マシンを作成するには、次の手順に従います。

1. Visual Studio の **[ファイル]** メニューから、**[新しいプロジェクト]**、**[Web]**、**[ASP.NET Web アプリケーション]** の順にクリックします。
2. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、必要な Web アプリケーションの種類を選択します。このダイアログ ボックスの Azure セクション (右下隅) にある **[クラウドでのホスト]** チェック ボックスがオンになっていることを確認してください (インストールによっては、このチェック ボックスに **"リモート リソースの作成"** というラベルが付いている場合もあります)。

	![][0]

3. **[仮想マシン]** をクリックし、さらに **[OK]** をクリックします。
4. Azure へのサインインを要求された場合には、サインインしてください。[仮想マシンを作成する] ダイアログ ボックスが表示されます。

	![][2]

5. [DNS 名] ボックスに仮想マシンの名前を入力します。DNS 名は、Azure 上で一意の名前にする必要があります。入力した名前が利用できない場合には、赤色の感嘆符が表示されます。
6. [イメージ] ボックスの一覧から、仮想マシンで使用するオペレーティング システムのイメージを選択します。標準のイメージだけでなく、Azure にアップロードしたイメージを選択することもできます。
7. IIS 以外の Web サーバーをインストールする場合を除き、**[IIS と Web 配置を有効にする]** チェック ボックスはオンのままにしておきます。Web 配置を無効にすると、Visual Studio からの発行ができません。IIS と Web 配置は、パッケージ化された Windows Server のイメージ (カスタム イメージも含む) に追加できます。
8. **[サイズ]** ボックスの一覧から、仮想マシンのサイズを選択します。
9. この仮想マシンのログイン資格情報を指定します。この情報は、リモート デスクトップからこの仮想マシンにアクセスする際に必要なため、メモしておきます。
10. **[場所]** ボックスの一覧から、仮想マシンをホストするリージョン、仮想ネットワーク、またはアフィニティ グループを選択します。アフィニティ グループを使用すると、相互に大量のネットワーク トラフィックがある Azure のリソースを同じデータセンター内に配置できます。また、リージョンを使用すると、データセンターの実際の場所を指定できます。
11. **[OK]** をクリックして、仮想マシン作成プロセスを開始します。**[出力]** ウィンドウで進捗状況を確認できます。

	![][3]

12. 仮想マシンがプロビジョニングされている間に、ソリューションの **PublishScripts** ノード内で発行スクリプトが作成されます。発行スクリプトの実行により Azure 内で仮想マシンがプロビジョニングされます。**[出力]** ウィンドウに状態が表示されます。このスクリプトは、仮想マシンを作成するために以下の操作を行います。

	* 既存の仮想マシンがない場合は、作成します。
	* `devtest` で始まる名前のストレージ アカウントを作成します。ただし、指定リージョンにこのストレージ アカウントがない場合に限ります。
	* 仮想マシン用のコンテナーとしてクラウド サービスを作成し、Web サイト用の Web ロールを作成します。
	* 仮想マシンで Web 配置を構成します。
	* 仮想マシンで IIS および ASP.NET を構成します。

	![][4]

<br/>
13. (必要に応じて) **サーバー エクスプローラー**で、**[仮想マシン]** ノードを展開し、作成した仮想マシンのノードをクリックします。さらに、**[リモート デスクトップを使用して接続する]** をクリックして仮想マシンに接続します。

# 次のステップ

発行スクリプトをカスタマイズする場合の詳細については、[こちら](http://msdn.microsoft.com/library/dn642480.aspx)を参照してください。

[0]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_CreateVM.PNG
[3]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_Provisioning.png
[4]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SolutionExplorer.png

<!--HONumber=45--> 
