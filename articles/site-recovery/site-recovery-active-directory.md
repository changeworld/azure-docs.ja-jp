<properties
	pageTitle="Azure Site Recovery で Active Directory と DNS を保護する | Microsoft Azure"
	description="この記事では、Azure Site Recovery を使って Active Directory の障害復旧ソリューションを実装する方法について説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/10/2016"
	ms.author="pratshar"/>

# Azure Site Recovery で Active Directory と DNS を保護する

SharePoint、Dynamics AX、SAP などのエンタープライズ アプリケーションは、Active Directory と DNS インフラストラクチャを基盤として正常な動作が実現されています。アプリケーションの障害復旧ソリューションを作成するときに重要なことは、Active Directory と DNS を保護しておき、他のアプリケーション コンポーネントよりも先に復旧させる必要があるということです。そうしないと、障害が発生したときにアプリケーションが正しく機能しません。

Site Recovery は、仮想マシンのレプリケーション、フェールオーバー、復旧を指揮することによって障害復旧機能をつかさどる Azure サービスです。Site Recovery は、仮想マシンとアプリケーションの一貫した保護、およびプライベート/パブリック クラウドまたはホスト側のクラウドへのシームレスなフェールオーバーを実行するための多くのレプリケーション シナリオをサポートしています。

Site Recovery を使用すると、Active Directory に合わせて完全な自動障害復旧計画を作成できます。障害が発生した場合、どこからでも数秒でフェールオーバーを開始し、数分以内に Active Directory を稼働させることができます。プライマリ サイトで複数のアプリケーション (SharePoint と SAP など) 用に Active Directory をデプロイした場合で、かつサイト全体をフェールオーバーする必要がある場合、Site Recovery を使って先に Active Directory をフェールオーバーした後、アプリケーションごとの復旧計画を使って他のアプリケーションをフェールオーバーすることができます。

この記事では、Active Directory 用の障害復旧ソリューションの作成方法や、ワンクリック復旧計画を使用した計画済み、計画外、テスト フェールオーバーの実行方法、サポートされている構成と前提条件について説明します。先に進む前に、Active Directory と Azure Site Recovery について理解しておく必要があります。

推奨される選択肢は 2 つあり、実際の環境の複雑さに応じて使い分けてください。

### 方法 1

アプリケーションの数が少なく、ドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery を使用して、(フェールオーバー先が Azure であるかセカンダリ サイトであるかに関係なく) セカンダリ サイトにドメイン コントローラーをレプリケートすることをお勧めします。レプリケートされた同じ仮想マシンをテスト フェールオーバー用にも使用できます。

### 方法 2

環境内に多数のアプリケーションがあってドメイン コントローラーが複数存在する場合、または少数のアプリケーションを一度にフェールオーバーすることを検討している場合は、Site Recovery でドメイン コントローラーの仮想マシンをレプリケートすることに加え、別のドメイン コントローラーをターゲット サイト (Azure またはセカンダリのオンプレミス データセンター) にセットアップすることをお勧めします。

>[AZURE.NOTE] 方法 2 を選んだ場合でも、テスト フェールオーバーを実行するためには、Site Recovery を使ってドメイン コントローラーをレプリケートする必要があります。詳細については、「[テスト フェールオーバーの考慮事項](#considerations-for-test-failover)」を参照してください。


以降のセクションでは、Site Recovery でドメイン コントローラーの保護を有効にする方法と、Azure でドメイン コントローラーをセットアップする方法について説明します。


## 前提条件

- Active Directory と DNS サーバーのオンプレミス デプロイ。
- Microsoft Azure サブスクリプションにおける Azure Site Recovery Services の資格情報コンテナー。
- レプリケート先が Azure である場合、Azure の仮想マシン準備状況評価ツールを VM で実行し、Azure VM と Azure Site Recovery Services に互換性があることを確認してください。


## Site Recovery を使用して保護を有効にする


### 仮想マシンの保護

Site Recovery でドメイン コントローラー/DNS 仮想マシンの保護を有効にします。仮想マシンの種類 (Hyper-V または VMware) に応じて Site Recovery の設定を構成します。クラッシュ整合性のレプリケーション間隔には 15 分をお勧めします。

###仮想マシンのネットワーク設定の構成

ドメイン コントローラー/DNS 仮想マシンに関して、フェールオーバー後、VM が適切なネットワークに接続されるように Site Recovery でネットワーク設定を構成します。たとえば、Hyper-V の VM を Azure にレプリケートする場合、VMM クラウド内の VM または保護グループ内の VM を選択して、以下のようにネットワーク設定を構成することができます。

![VM ネットワークの設定](./media/site-recovery-active-directory/VM-Network-Settings.png)

## Active Directory レプリケーションで Active Directory を保護する

### サイト間の保護

セカンダリ サイトにドメイン コントローラーを作成し、そのサーバーの役割をドメイン コントローラーに昇格させるときに、プライマリ サイト側と同じドメイン名を指定します。**Active Directory サイトとサービス** スナップインを使用して、サイトの追加先となるサイト リンク オブジェクトに対する設定を構成することができます。サイト リンクで設定を構成することで、2 つまたはそれ以上のサイト間でレプリケーションを発生させる時間と頻度を制御できます。詳細については、「[サイト間でのレプリケーションをスケジュールする](https://technet.microsoft.com/library/cc731862.aspx)」を参照してください。

###サイトと Azure 間の保護

次の手順に従って、[Azure 仮想ネットワークにドメイン コントローラー](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)を作成します。サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側と同じドメイン名を指定してください。

その後、Azure で DNS サーバーを使用するには、[仮想ネットワークの DNS サーバーを再構成](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)します。

![Azure ネットワーク](./media/site-recovery-active-directory/azure-network.png)

## テスト フェールオーバーの考慮事項

テスト フェールオーバーは、運用環境のワークロードに影響が生じないよう、運用ネットワークから分離されたネットワークで行われます。

また、ほとんどのアプリケーションは、ドメイン コントローラーと DNS サーバーがないと正常に機能しません。そのため、アプリケーションのフェールオーバーを実行する前に、テスト フェールオーバー用の分離されたネットワーク内にドメイン コントローラーを作成しておく必要があります。最も簡単な方法としては、ドメイン コントローラー/DNS 仮想マシンに対する保護を Site Recovery で有効にし、その仮想マシンのテスト フェールオーバーを実行した後で、アプリケーション用の復旧計画のテスト フェールオーバーを実行します。その方法は次のとおりです。

1. Site Recovery でドメイン コントローラー/DNS 仮想マシンの保護を有効にします。
2. 分離されたネットワークを作成します。既定では、Azure で作成されるすべての仮想ネットワークは、その他のネットワークから分離します。このネットワークの IP アドレス範囲は、運用ネットワークと同じものを使用することをお勧めします。このネットワーク上でサイト間接続を有効化しないでください。
3. DNS 仮想マシンに取得させる IP アドレスとして、作成したネットワークに DNS の IP アドレスを指定します。レプリケート先として Azure を使用している場合は、フェールオーバーで使用する VM の IP アドレスを VM プロパティの **[ターゲット IP]** 設定で指定します。別のオンプレミス サイトにレプリケートしていて、かつ DHCP を使用している場合は、所定の手順に従って、[テスト フェールオーバー用に DNS と DHCP をセットアップ](site-recovery-failover.md#prepare-dhcp)してください。

>[AZURE.NOTE] テスト フェールオーバー中に仮想マシンに割り当てられた IP アドレスは、テスト フェールオーバー ネットワークで利用可能である場合、計画されたフェールオーバーや計画されていないフェールオーバー中に取得される IP アドレスと同じです。その IP アドレスが利用できない場合は、テスト フェールオーバー ネットワーク内で利用できる他の IP アドレスが仮想マシンに割り当てられます。

4. ドメイン コントローラーの仮想マシンで、分離されたネットワークでテスト フェールオーバーを実行します。
5. アプリケーション復旧計画のテスト フェールオーバーを実行します。
6. テストが完了したら、Site Recovery ポータルの **[ジョブ]** タブで、ドメイン コントローラー仮想マシンのジョブと復旧計画とに対するテスト フェールオーバーを "完了" としてマークします。

### DNS とドメイン コントローラーが異なるマシン上に存在する場合

DNS がドメイン コントローラーと同じ仮想マシン上にない場合、テスト フェールオーバー用の DNS VM を作成する必要があります。両者が同じ VM 上にある場合は、このセクションを省略できます。

新規の DNS サーバーを使用し、必要なすべてのゾーンを作成することができます。たとえば、Active Directory ドメインが contoso.com である場合には、contoso.com という名前で DNS ゾーンを作成することができます。Active Directory に対応するエントリは DNS で次のように更新する必要があります。

1. 復旧計画内の他のすべての仮想マシンが起動する前に、次の設定が有効になっているようにします。

	- ゾーンは、フォレスト ルート名に従って名前を付ける必要があります。
	- ゾーンは、ファイルに格納される必要があります。
	- ゾーンは、セキュリティ保護された更新とセキュリティ保護されていない更新の両方に対して有効である必要があります。
	- ドメイン コントローラーの仮想マシンのリゾルバーは、DNS の仮想マシンの IP アドレスをポイントする必要があります。

2. ドメイン コントローラーの仮想マシンのディレクトリで、次のコマンドを実行します。

	`nltest /dsregdns`

3. DNS サーバー上にゾーンを追加し、セキュリティで保護されていない更新を許可し、その DNS サーバー用のエントリを DNS に追加します。

	    dnscmd /zoneadd contoso.com  /Primary
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
	    dnscmd /config contoso.com /allowupdate 1


## 次のステップ

Azure Site Recovery によるエンタープライズ ワークロード保護の詳細については、「[Azure Site Recovery で保護できるワークロード](../site-recovery/site-recovery-workload.md)」を参照してください。

<!---HONumber=AcomDC_0511_2016-->