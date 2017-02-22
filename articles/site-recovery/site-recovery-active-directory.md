---
title: "Azure Site Recovery で Active Directory と DNS を保護する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使って Active Directory の障害復旧ソリューションを実装する方法について説明します。"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/9/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: feb0200fc27227f546da8c98f21d54f45d677c98
ms.openlocfilehash: a583225b4f3acd747a10c1c1fd337bc1b7ac599c


---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Azure Site Recovery で Active Directory と DNS を保護する
SharePoint、Dynamics AX、SAP などのエンタープライズ アプリケーションは、Active Directory と DNS インフラストラクチャを基盤として正常な動作が実現されています。 アプリケーションの障害復旧ソリューションを作成するときに重要なことは、Active Directory と DNS を保護しておき、他のアプリケーション コンポーネントよりも先に復旧させる必要があるということです。そうしないと、障害が発生したときにアプリケーションが正しく機能しません。

Site Recovery は、仮想マシンのレプリケーション、フェールオーバー、復旧を調整することで障害復旧機能を実現する Azure サービスです。 Site Recovery は、仮想マシンとアプリケーションの一貫した保護、およびプライベート/パブリック クラウドまたはホスト側のクラウドへのシームレスなフェールオーバーを実行するための多くのレプリケーション シナリオをサポートしています。

Site Recovery を使用すると、Active Directory に合わせて完全な自動障害復旧計画を作成できます。 障害が発生した場合、どこからでも数秒でフェールオーバーを開始し、数分以内に Active Directory を稼働させることができます。 プライマリ サイトで複数のアプリケーション (SharePoint と SAP など) 用に Active Directory をデプロイした場合で、かつサイト全体をフェールオーバーする必要がある場合、Site Recovery を使って先に Active Directory をフェールオーバーした後、アプリケーションごとの復旧計画を使って他のアプリケーションをフェールオーバーすることができます。

この記事では、Active Directory 用の障害復旧ソリューションの作成方法や、ワンクリック復旧計画を使用した計画済み、計画外、テスト フェールオーバーの実行方法、サポートされている構成と前提条件について説明します。  先に進む前に、Active Directory と Azure Site Recovery について理解しておく必要があります。

## <a name="replicating-domain-controller"></a>ドメイン コントローラーのレプリケート

ドメイン コントローラーと DNS をホストしている少なくとも&1; つの仮想マシンで、[Site Recovery レプリケーション](#enable-protection-using-site-recovery)をセットアップする必要があります。 環境に[複数のドメイン コントローラー](#environment-with-multiple-domain-controllers)がある場合は、Site Recovery でドメイン コントローラー仮想マシンをレプリケートするだけでなく、[追加のドメイン コントローラー](#protect-active-directory-with-active-directory-replication)をターゲット サイト (Azure またはセカンダリ オンプレミス データセンター) にセットアップする必要もあります。 

### <a name="single-domain-controller-environment"></a>ドメイン コントローラーが&1; 台の環境
アプリケーションの数が少なく、ドメイン コントローラーが&1; つしかない場合にサイト全体をフェールオーバーするには、Site Recovery を使って、(フェールオーバー先が Azure であるかセカンダリ サイトであるかに関係なく) セカンダリ サイトにドメイン コントローラーをレプリケートすることをお勧めします。 レプリケートされた同じドメイン コントローラー/DNS 仮想マシンを、[テスト フェールオーバー](#test-failover-considerations)用にも使うことができます。

### <a name="environment-with-multiple-domain-controllers"></a>複数のドメイン コントローラーがある環境
環境内にアプリケーションが多数あってドメイン コントローラーが複数存在する場合、または少数のアプリケーションを一度にフェールオーバーすることを検討している場合は、Site Recovery でドメイン コントローラーの仮想マシンをレプリケートすることに加え、[別のドメイン コントローラー](#protect-active-directory-with-active-directory-replication)をターゲット サイト (Azure またはセカンダリのオンプレミス データセンター) にセットアップすることをお勧めします。 このシナリオでは、Site Recovery によってレプリケートされるドメイン コントローラーを[テスト フェールオーバー](#test-failover-considerations)のために使い、フェールオーバーを行うときはターゲット サイト上の追加ドメイン コントローラーを使います。 


以降のセクションでは、Site Recovery でドメイン コントローラーの保護を有効にする方法と、Azure でドメイン コントローラーをセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件
* Active Directory と DNS サーバーのオンプレミス デプロイ。
* Microsoft Azure サブスクリプションにおける Azure Site Recovery Services の資格情報コンテナー。
* レプリケート先が Azure である場合、Azure の仮想マシン準備状況評価ツールを VM で実行し、Azure VM と Azure Site Recovery Services に互換性があることを確認してください。

## <a name="enable-protection-using-site-recovery"></a>Site Recovery を使用して保護を有効にする
### <a name="protect-the-virtual-machine"></a>仮想マシンの保護
Site Recovery でドメイン コントローラー/DNS 仮想マシンの保護を有効にします。 仮想マシンの種類 (Hyper-V または VMware) に応じて Site Recovery の設定を構成します。 クラッシュ整合性のレプリケーション間隔には 15 分をお勧めします。

### <a name="configure-virtual-machine-network-settings"></a>仮想マシンのネットワーク設定の構成
ドメイン コントローラー/DNS 仮想マシンに関して、フェールオーバー後、VM が適切なネットワークに接続されるように Site Recovery でネットワーク設定を構成します。 たとえば、Hyper-V の VM を Azure にレプリケートする場合、VMM クラウド内の VM または保護グループ内の VM を選択して、以下のようにネットワーク設定を構成することができます。

![VM ネットワークの設定](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Active Directory レプリケーションで Active Directory を保護する
### <a name="site-to-site-protection"></a>サイト間の保護
セカンダリ サイトにドメイン コントローラーを作成し、そのサーバーの役割をドメイン コントローラーに昇格させるときに、プライマリ サイト側と同じドメイン名を指定します。 **Active Directory サイトとサービス** スナップインを使用して、サイトの追加先となるサイト リンク オブジェクトに対する設定を構成することができます。 サイト リンクで設定を構成することで、2 つまたはそれ以上のサイト間でレプリケーションを発生させる時間と頻度を制御できます。 詳細については、「 [サイト間でのレプリケーションをスケジュールする](https://technet.microsoft.com/library/cc731862.aspx) 」を参照してください。

### <a name="site-to-azure-protection"></a>サイトと Azure 間の保護
次の手順に従って、 [Azure 仮想ネットワークにドメイン コントローラー](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)を作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側と同じドメイン名を指定してください。

その後、Azure で DNS サーバーを使用するには、 [仮想ネットワークの DNS サーバーを再構成](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)します。

![Azure ネットワーク](./media/site-recovery-active-directory/azure-network.png)

**Azure 運用ネットワークの DNS**

## <a name="test-failover-considerations"></a>テスト フェールオーバーの考慮事項
テスト フェールオーバーは、運用環境のワークロードに影響が生じないよう、運用ネットワークから分離されたネットワークで行われます。

また、ほとんどのアプリケーションは、ドメイン コントローラーと DNS サーバーがないと正常に機能しません。そのため、アプリケーションのフェールオーバーを実行する前に、テスト フェールオーバー用の分離されたネットワーク内にドメイン コントローラーを作成しておく必要があります。 最も簡単な方法としては、ドメイン コントローラー/DNS 仮想マシンに対する保護を Site Recovery で有効にし、その仮想マシンのテスト フェールオーバーを実行した後で、アプリケーション用の復旧計画のテスト フェールオーバーを実行します。 その方法は次のとおりです。

1. Site Recovery でドメイン コントローラー/DNS 仮想マシンの保護を有効にします。
1. 分離されたネットワークを作成します。 既定では、Azure で作成されるすべての仮想ネットワークは、その他のネットワークから分離します。 このネットワークの IP アドレス範囲は、運用ネットワークと同じものを使用することをお勧めします。 このネットワーク上でサイト間接続を有効化しないでください。
1. DNS 仮想マシンに取得させる IP アドレスとして、作成したネットワークに DNS の IP アドレスを指定します。 レプリケート先として Azure を使っている場合は、フェールオーバーで使われる VM の IP アドレスを **[コンピューティングとネットワーク]** の設定の **[ターゲット IP]** の設定で指定します。 

    ![ターゲット IP](./media/site-recovery-active-directory/DNS-Target-IP.png)
    **ターゲット IP**

    ![Azure テスト ネットワーク](./media/site-recovery-active-directory/azure-test-network.png)

    **Azure テスト ネットワークの DNS**

1. 別のオンプレミス サイトにレプリケートしていて、かつ DHCP を使用している場合は、所定の手順に従って、 [テスト フェールオーバー用に DNS と DHCP をセットアップ](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. 分離されたネットワークで実行されているドメイン コントローラー仮想マシンのテスト フェールオーバーを行います。 テスト フェールオーバーの実行には、ドメイン コントローラー仮想マシンにある**アプリケーション整合性**復旧ポイントのうち最新のものを使用します。 
1. アプリケーション復旧計画のテスト フェールオーバーを実行します。
1. テストが完了したら、Site Recovery ポータルの **[ジョブ]** タブで、ドメイン コントローラー仮想マシンと復旧計画のそれぞれのテスト フェールオーバー ジョブを "完了" としてマークします。


> [!TIP]
> テスト フェールオーバー中に仮想マシンに割り当てられた IP アドレスは、テスト フェールオーバー ネットワークで利用可能である場合、計画されたフェールオーバーや計画されていないフェールオーバー中に取得される IP アドレスと同じです。 その IP アドレスが利用できない場合は、テスト フェールオーバー ネットワーク内で利用できる他の IP アドレスが仮想マシンに割り当てられます。
> 
> 


### <a name="removing-reference-to-other-domain-controllers"></a>その他のドメイン コントローラーへの参照を削除する
テスト フェールオーバーを実行する場合、テスト用ネットワークですべてのドメイン コントローラーを使用するわけではありません。 運用環境に存在する他のドメイン コント ローラーの参照を削除するには、使用しないドメイン コントローラーに [Active Directory の FSMO 役割を強制し、メタデータのクリーンアップを実行する](http://aka.ms/ad_seize_fsmo)必要があります。 

### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>テスト フェールオーバーの間のドメイン コントローラーの問題のトラブルシューティング


コマンド プロンプトで次のコマンドを実行して、SYSVOL フォルダーと NETLOGON フォルダーが共有されているかどうかを確認します。

    NET SHARE

コマンド プロンプトで次のコマンドを実行して、ドメイン コントローラーが正常に機能していることを確認します。

    dcdiag /v > dcdiag.txt

出力ログで次のテキストを探し、ドメイン コントローラーが正しく機能していることを確認します。 

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

上記の条件が満たされている場合、ドメイン コントローラーは正常に機能していると考えられます。 それ以外の場合は、次の手順を試します。


* ドメイン コントローラーの Authoritative Restore を実行します。
    * [FRS レプリケーションを使うことは推奨されません](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)が、まだ使っている場合は、[こちら](https://support.microsoft.com/en-in/kb/290762)で提供されている手順に従って、Authoritative Restore を行います。 前記のリンクで言及されている Burflags について詳しくは、[こちら](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)をご覧ください。
    * DFSR レプリケーションを使っている場合は、[こちら](https://support.microsoft.com/en-us/kb/2218556)の手順に従って、Authoritative Restore を行います。 また、[こちらのリンク](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)で説明されている Powershell 関数をこの目的に使うこともできます。 
    
* 次のレジストリ キーを 0 に設定して、初期同期要件をバイパスします。 この DWORD 値が存在しない場合は、"Parameters" ノードの下に作成できます。 詳しくは、[こちら](https://support.microsoft.com/en-us/kb/2001093)をご覧ください。

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* 次のレジストリ キーを 1 に設定して、ユーザーのログオンを検証するためにグローバル カタログ サーバーを利用する要件を無効にします。 この DWORD 値が存在しない場合は、"Lsa" ノードの下に作成できます。 詳しくは、[こちら](http://support.microsoft.com/kb/241789/EN-US)をご覧ください。

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS とドメイン コントローラーが異なるマシン上に存在する場合
DNS がドメイン コントローラーと同じ仮想マシン上にない場合、テスト フェールオーバー用の DNS VM を作成する必要があります。 両者が同じ VM 上にある場合は、このセクションを省略できます。

新規の DNS サーバーを使用し、必要なすべてのゾーンを作成することができます。 たとえば、Active Directory ドメインが contoso.com である場合には、contoso.com という名前で DNS ゾーンを作成することができます。 Active Directory に対応するエントリは DNS で次のように更新する必要があります。

1. 復旧計画内の他のすべての仮想マシンが起動する前に、次の設定が有効になっているようにします。
   
   * ゾーンは、フォレスト ルート名に従って名前を付ける必要があります。
   * ゾーンは、ファイルに格納される必要があります。
   * ゾーンは、セキュリティ保護された更新とセキュリティ保護されていない更新の両方に対して有効である必要があります。
   * ドメイン コントローラーの仮想マシンのリゾルバーは、DNS の仮想マシンの IP アドレスをポイントする必要があります。
2. ドメイン コントローラーの仮想マシンで、次のコマンドを実行します。
   
    `nltest /dsregdns`
3. DNS サーバー上にゾーンを追加し、セキュリティで保護されていない更新を許可し、その DNS サーバー用のエントリを DNS に追加します。
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>次のステップ
Azure Site Recovery によるエンタープライズ ワークロード保護の詳細については、「[Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)」をご覧ください。




<!--HONumber=Jan17_HO4-->


