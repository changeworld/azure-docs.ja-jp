<properties 
	pageTitle="Azure の仮想ネットワークでの Active Directory フォレストのインストール" 
	description="Azure の仮想ネットワーク上の仮想マシン (VM) に新しい Active Directory フォレストを作成する手順について説明したチュートリアルです。" 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="03/04/2015" 
	ms.author="Justinha"/>


# Azure の仮想ネットワークでの Active Directory フォレストのインストール

このトピックでは、[Azure の仮想ネットワーク](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)上の仮想マシン (VM) に新しい Windows Server Active Directory 環境を作成する方法を示します。この場合、Azure の仮想ネットワークは内部設置型のネットワークには接続されません。 

必要に応じて次の関連するトピックもご覧ください。

- [管理ポータル ウィザードを使用してサイト間 VPN を構成](http://msdn.microsoft.com/library/windowsazure/dn133795.aspx)した後、新しいフォレストをインストールするか、内部設置型のフォレストを Azure の仮想ネットワークに拡張することもできます。これらの手順については、「[Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](virtual-networks-install-replica-active-directory-domain-controller.md)」をご覧ください。
-  Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)」をご覧ください。


## 内部設置型の場合との違い

ドメイン コントローラーの内部設置型へのインストールと Azure へのインストールにそれほど大きな違いはありません。主な違いを次の表に示します。 

構成対象|内部設置型  |Azure の仮想ネットワーク	
------------- | -------------  | ------------
**ドメイン コントローラーの IP アドレス**  | ネットワーク アダプターのプロパティの静的 IP アドレスを割り当てる   | Set-AzureStaticVNetIP コマンドレットを実行して静的 IP アドレスを割り当てる
**DNS クライアント リゾルバー** |ドメイン メンバーのネットワーク アダプターのプロパティの優先と代替 DNS サーバー アドレスを設定する   |仮想ネットワークのプロパティの DNS サーバー アドレスを設定する
**Active Directory データベース ストレージ**  | 既定の保存先を C:\ から変更する (省略可能)|既定の保存先を C:\ から変更する (必須)



## Azure の仮想ネットワークを作成する

1. Azure 管理ポータルにサインインします。
2. 仮想ネットワークを作成します。**[ネットワーク]**、**[仮想ネットワークの作成]** の順にクリックします。次の表の値を使用してウィザードの手順を完了します。 

	ウィザードのページ  | 指定する値
	------------- | -------------
	**仮想ネットワークの詳細**  | <p>名前:仮想ネットワークの名前を入力する</p><p>リージョン:最寄りのリージョンを選択する</p>
	**DNS と VPN**  | <p>[DNS サーバー] は空白のままにする</p><p>いずれの VPN オプションも選択しない</p>
	**仮想ネットワーク アドレス空間**  | <p>サブネット名:サブネットの名前を入力する</p><p>開始 IP:<b>10.0.0.0</b></p><p>CIDR:<b>/24 (256)</b></p>



## ドメイン コントローラーと DNS サーバーのロールを実行する仮想マシンを作成する
 
次の手順を繰り返して、必要に応じて、DC ロールをホストする VM を作成します。フォールト トレランスと冗長性を確保するには 2 つ以上の仮想 DC をデプロイする必要があります。Azure の仮想ネットワークに、類似した構成の DC (いずれも GCで、DNS サーバーを実行し、FSMO ロールを保持していないなど) が 2 つ以上あると、それらの DC を実行する仮想マシンを可用性セットに置くとフォールト トレランスが向上します。

1. Azure 管理ポータルで、**[新規]** > **[コンピューティング]** > **[仮想マシン]** > **[ギャラリーから]** の順にクリックします。次の値を使用して、ウィザードを完了します。別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。

    ウィザードのページ  |  指定する値
	------------- | -------------
	**イメージの選択**  | Windows Server 2012 R2 Datacenter
	**仮想マシンの構成**  | <p>仮想マシン名:単一ラベルの名前 (例: AzureDC1) を入力します。</p><p>新しいユーザー名:ユーザーの名前を入力します。このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。最初に VM にサインインするときは、この名前でサインインする必要があります。Administrator という名前の組み込みのアカウントは機能しません。</p><p>新しいパスワード/確認:パスワードを入力します。</p>
	**仮想マシンの構成**  | <p>クラウド サービス:1 台目の VM の作成時には<b>[新しいクラウド サービスの作成]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</p><p>クラウド サービス DNS 名:グローバルに一意の名前を指定します。</p><p>リージョン/アフィニティ グループ/仮想ネットワーク:仮想ネットワーク名 (例: WestUSVNet) を指定します。</p><p>ストレージ アカウント:1 台目の VM の作成時には <b>[自動的に生成されたストレージ アカウントを使用]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じストレージ アカウント名を選択します。</p><p>可用性セット:<b>[可用性セットの作成]</b> を選択します。</p><p>可用性セット名:1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</p>
	**仮想マシンの構成**  | <p><b>[VM エージェントのインストール]</b> と、必要な他の拡張機能を選択します。</p>
2. DC サーバーのロールを実行する各 VM にディスクを接続します。AD データベース、ログ、SYSVOL を格納するには、追加のディスクが必要です。ディスクのサイズを指定して (10 GB など)、**[ホスト キャッシュ設定]** は **[None]** のままにします。初めて VM にサインインした後、**[サーバー マネージャー]** > **[ファイル サービスとストレージ サービス]** の順に開いて、NTFS を使用してこのディスクにボリュームを作成します。
3. DC ロールを実行する VM の静的 IP アドレスを予約します。静的 IP アドレスを予約するには、Microsoft Web Platform Installer をダウンロードして、[Azure PowerShell をインストール](powershell-install-configure.md) し、Set-AzureStaticVNetIP コマンドレットを実行します。次に例を示します。

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

静的 IP アドレスを設定する方法の詳細については、「[VM 用の静的内部 IP アドレスを構成する](https://msdn.microsoft.com/library/azure/dn630228.aspx)」をご覧ください。

## Windows Server Active Directory をインストールする

同じルーチンを使用して、オンプレミスで使用する [AD DSをインストール](https://technet.microsoft.com/library/jj574166.aspx)します (UI、応答ファイルや Windows PowerShell を使用できるようになります)。管理者の資格情報を指定して新しいフォレストをインストールする必要があります。Active Directory データベースの場所、ログ、SYSVOL を指定するには、既定の保存先をオペレーティング システム ドライブから、VM にアタッチした追加のデータ ディスクに変更します。 

DC のインストールが完了したら、仮想マシンに再び接続し、DC にログオンします。必ずドメインの資格情報の指定してください。

## Azure 仮想ネットワークの DNS サーバーをリセットする

1. 新しい DC/DNS サーバーでDNS フォワーダー設定をリセットします。 
  1. サーバー マネージャーで、**[ツール]** > **[DNS]** の順にクリックします。 
  2. **[DNS マネージャー]** で、DNS サーバーを右クリックして **[プロパティ]** をクリックします。 
  3. **[フォワーダー]** タブで、フォワーダーの IP アドレスをクリックして **[編集]** をクリックします。IP アドレスを選択して、**[削除]** をクリックします。 
  4. **[OK]** をクリックしてエディターを閉じ、もう一度 **[OK]** をクリックして DNS サーバーのプロパティを閉じます。 
2. 仮想ネットワークの DNS サーバーの設定を更新します。 
  1. **[仮想ネットワーク]** をクリックして、作成した仮想ネットワークをダブルクリックし、**[構成]** > **[DNS サーバー]** の順にクリックします。DC/DNS サーバー ロールを実行する仮想マシンの名前と DIP を入力して、**[保存]** をクリックします。 
  2. VM を選択し、**[再起動]** をクリックすると、新しい DNS サーバーの IP アドレスで DNS リゾルバーの設定が VM で構成されます。 


## ドメイン メンバーの VM を作成する

1. アプリケーション サーバーとして実行する VM を作成するには、次の手順を繰り返します。別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。

	ウィザードのページ  | 指定する値
	------------- | -------------
	**イメージの選択**  | Windows Server 2012 R2 Datacenter
	**仮想マシンの構成**  | <p>仮想マシン名:単一ラベルの名前 (例: AppServer1) を入力します。</p><p>新しいユーザー名:ユーザーの名前を入力します。このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。最初に VM にサインインするときは、この名前でサインインする必要があります。Administrator という名前の組み込みのアカウントは機能しません。</p><p>新しいパスワード/確認:パスワードを入力します。</p>
	**仮想マシンの構成**  | <p>クラウド サービス:1 台目の VM の作成時には **[新しいクラウド サービスの作成]** を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</p><p>クラウド サービス DNS 名:グローバルに一意の名前を指定します。</p><p>リージョン/アフィニティ グループ/仮想ネットワーク:仮想ネットワーク名 (例: WestUSVNet) を指定します。</p><p>ストレージ アカウント:1 台目の VM の作成時には **[自動的に生成されたストレージ アカウントを使用]** を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じストレージ アカウント名を選択します。</p><p>可用性セット:**[可用性セットの作成]** を選択します。</p><p>可用性セット名:1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</p>
	**仮想マシンの構成**  | <p><b>[VM エージェントのインストール]</b> と、必要な他の拡張機能を選択します。</p>
2. 各 VM がプロビジョニングされたら、サインインし、ドメインに参加させます。**サーバー マネージャー** で、**[ローカル サーバー]** > **[ワークグループ]** > **[変更...]** の順にクリックして、**[ドメイン]** を選択し、内部設置型ドメインの名前を入力します。ドメイン ユーザーの資格情報を入力し、VM を再起動して、ドメインへの参加を完了します。

VM のプロビジョニングには、管理ポータルを使用する代わりに、Microsoft Azure の Windows PowerShell を使用することもできます。最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、 [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx) と [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) を使用し、VM を作成するには [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) を使用します。 


Windows PowerShell の使い方の詳細については、「[Azure コマンドレットの概要](https://msdn.microsoft.com/library/azure/jj554332.aspx)」と「[Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/jj554330.aspx)」をご覧ください。


## 関連項目

-  [Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [管理ポータルでのクラウド専用仮想ネットワークの構成](https://msdn.microsoft.com/library/dn631643.aspx)

-  [管理ポータルでのサイト間 VPN の構成](https://msdn.microsoft.com/library/dn133795.aspx)

-  [Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](virtual-networks-install-replica-active-directory-domain-controller.md)

-  [Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [仮想ネットワークの概要](https://msdn.microsoft.com/library/azure/jj156007.aspx)

-  [Azure PowerShell のインストールと構成方法](powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set Azure VM Static IP Address (Azure VM の静的 IP アドレスの設定)](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [How to assign Static IP to Azure VM (静的 IP アドレスを Azure VM に割り当てる方法)](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Windows Server 2012 の新しい Active Directory フォレストをインストールする](https://technet.microsoft.com/library/jj574166.aspx)

-  [Active Directory ドメイン サービス (AD DS) の仮想化 (レベル 100) の概要](https://technet.microsoft.com/library/hh831734.aspx)

-  [Test Lab Guide: (テスト ラボ ガイド:)Windows Server 2012 R2 Base Configuration in Azure (Azure での Windows Server 2012 R2 の基本構成)](http://www.microsoft.com/download/details.aspx?id=41684)


<!--HONumber=47-->
