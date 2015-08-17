<properties
	pageTitle="Visual Studio を使用して Web プロジェクト用の仮想マシンを作成する"
	description="Web サイト用の仮想マシンを作成します。"
	services="virtual-machines"
	documentationCenter=""
	authors="kempb"
	manager="douge"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="kempb"/>

# Visual Studio で Web サイト用の仮想マシンを作成する

Azure の Web サイト用の Web プロジェクトを作成する際に、Azure 内で仮想マシンをプロビジョニングできます。さらに、他のソフトウェアで仮想マシンを構成したり、診断やデバッグのために仮想マシンを使用したりすることができます。

Web サイトを作成する際に仮想マシンを作成するには、次の手順に従います。

1. Visual Studio で、**[ファイル]**、**[新しいプロジェクト]**、**[Web]**、(**[Visual C#]** ノードまたは **[Visual Basic]** ノードの) **[ASP.NET Web アプリケーション]** の順にクリックします。
2. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、必要な Web アプリケーションの種類を選択します。このダイアログ ボックスの Azure セクション (右下隅) にある **[クラウドでのホスト]** チェック ボックスがオンになっていることを確認してください (インストールによっては、このチェック ボックスに **"リモート リソースの作成"** というラベルが付いている場合もあります)。

	![][0]

3. [Microsoft Azure] の下のボックスの一覧の **[仮想マシン]** をクリックし、**[OK]** をクリックします。
4. サインインを要求されたら Azure にサインインします。**[仮想マシンを作成する]** ダイアログ ボックスが表示されます。

	![][2]

5. [DNS 名] ボックスに仮想マシンの名前を入力します。DNS 名は、Azure 上で一意の名前にする必要があります。入力した名前が利用できない場合には、赤色の感嘆符が表示されます。
6. **[イメージ]** ボックスの一覧から、仮想マシンで使用する VM のイメージを選択します。標準の Azure VM イメージだけでなく、Azure にアップロードしたイメージを選択することもできます。
7. IIS 以外の Web サーバーをインストールする場合を除き、**[IIS と Web デプロイを有効にする]** チェック ボックスはオンのままにしておきます。Web デプロイを無効にすると、Visual Studio からの発行ができません。IIS と Web デプロイは、パッケージ化された Windows Server のイメージ (カスタム イメージも含む) に追加できます。
8. **[サイズ]** ボックスの一覧から、仮想マシンのサイズを選択します。
9. この仮想マシンのログイン資格情報を指定します。この情報は、リモート デスクトップからこの仮想マシンにアクセスする際に必要なため、メモしておきます。
10. **[場所]** ボックスの一覧で、仮想マシンをホストするリージョンを選択します。
11. **[OK]** をクリックして、仮想マシンの作成を開始します。操作の進捗状況は、**[出力]** ウィンドウに表示されます。

	![][3]

12. 仮想マシンがプロビジョニングされている間に、ソリューションの **PublishScripts** ノード内で発行スクリプトが作成されます。発行スクリプトの実行により Azure 内で仮想マシンがプロビジョニングされます。**[出力]** ウィンドウに状態が表示されます。このスクリプトは、仮想マシンを作成するために以下の操作を行います。

	* 既存の仮想マシンがない場合は、作成します。
	* `devtest` で始まる名前のストレージ アカウントを作成します。ただし、指定リージョンにこのストレージ アカウントがない場合に限ります。
	* 仮想マシン用のコンテナーとしてクラウド サービスを作成し、Web サイト用の Web ロールを作成します。
	* 仮想マシンで Web デプロイを構成します。
	* 仮想マシンで IIS および ASP.NET を構成します。

	![][4]

<br/> 13.(必要に応じて) 新しい仮想マシンに接続できます。**サーバー エクスプローラー**で、**[仮想マシン]** ノードを展開し、作成した仮想マシンのノードをクリックします。そのショートカット メニューで、**[リモート デスクトップを使用して接続する]** をクリックします。

 ![][5]


## 次のステップ

発行スクリプトをカスタマイズする場合の詳細については、[ここ](http://msdn.microsoft.com/library/dn642480.aspx)を参照してください。

[0]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/VS_Create_VM_Connect.png

<!---HONumber=August15_HO6-->