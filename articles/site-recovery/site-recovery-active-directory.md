---
title: "Azure Site Recovery で Active Directory と DNS を保護する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使って Active Directory のディザスター リカバリー ソリューションを実装する方法について説明します。"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: 5db2d424c176428d31fb99fd8288120f18fcac7a
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Azure Site Recovery で Active Directory と DNS を保護する
SharePoint、Dynamics AX、SAP などのエンタープライズ アプリケーションは、Active Directory と DNS インフラストラクチャを基盤として正常な動作が実現されています。 アプリケーションのディザスター リカバリー ソリューションを作成するときは、多くの場合、他のアプリケーション コンポーネントの前に Active Directory と DNS を復旧させる必要があります。そうすることで、アプリケーションが正しく機能します。

Site Recovery を使用すると、Active Directory に合わせて完全な自動ディザスター リカバリー計画を作成できます。 障害が発生した場合、どこからでも数秒でフェールオーバーを開始し、数分以内に Active Directory を稼働させることができます。 プライマリ サイトで複数のアプリケーション (SharePoint や SAP など) 用に Active Directory をデプロイした場合で、かつサイト全体をフェールオーバーする必要がある場合、Site Recovery を使って先に Active Directory をフェールオーバーした後、アプリケーションごとの復旧計画を使って他のアプリケーションをフェールオーバーすることができます。

この記事では、Active Directory 用のディザスター リカバリー ソリューションの作成方法、ワンクリック復旧計画を使用したフェールオーバーの実行方法、サポートされている構成と前提条件について説明します。  先に進む前に、Active Directory と Azure Site Recovery について理解しておく必要があります。

## <a name="prerequisites"></a>前提条件
* Microsoft Azure サブスクリプションにおける Recovery Services コンテナー。
* Azure にレプリケーションを行う場合、Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントなどの Azure リソースを[準備](tutorial-prepare-azure.md)します。
* すべてのコンポーネントのサポート要件を確認します。

## <a name="replicating-domain-controller"></a>ドメイン コントローラーのレプリケート

ドメイン コントローラーと DNS をホストしている少なくとも 1 つの仮想マシンで、[Site Recovery レプリケーション](#enable-protection-using-site-recovery)をセットアップする必要があります。 環境に[複数のドメイン コントローラー](#environment-with-multiple-domain-controllers)がある場合は、Site Recovery でドメイン コントローラー仮想マシンをレプリケートするだけでなく、[追加のドメイン コントローラー](#protect-active-directory-with-active-directory-replication)をターゲット サイト (Azure またはセカンダリ オンプレミス データセンター) にセットアップする必要もあります。

### <a name="single-domain-controller-environment"></a>ドメイン コントローラーが 1 台の環境
アプリケーションの数が少なく、ドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery を使ってターゲット サイト (Azure またはセカンダリ オンプレミス データセンター) にドメイン コントローラーをレプリケートすることをお勧めします。 レプリケートされた同じドメイン コントローラー/DNS 仮想マシンを、[テスト フェールオーバー](#test-failover-considerations)用にも使うことができます。

### <a name="environment-with-multiple-domain-controllers"></a>複数のドメイン コントローラーがある環境
環境内にアプリケーションが多数あってドメイン コントローラーが複数存在する場合、または少数のアプリケーションを一度にフェールオーバーすることを検討している場合は、Site Recovery でドメイン コントローラーの仮想マシンをレプリケートすることに加え、[別のドメイン コントローラー](#protect-active-directory-with-active-directory-replication)をターゲット サイト (Azure またはセカンダリ オンプレミス データセンター) にセットアップすることをお勧めします。 [テスト フェールオーバー](#test-failover-considerations)では Site Recovery によってレプリケートされたドメイン コントローラーを使用でき、実際のフェールオーバーではターゲット サイトの追加のドメイン コントローラーを使用できます。

## <a name="enable-protection-using-site-recovery"></a>Site Recovery を使用して保護を有効にする
### <a name="protect-the-virtual-machine"></a>仮想マシンの保護
Site Recovery でドメイン コントローラー/DNS 仮想マシンの保護を有効にします。 Site Recovery を使用してレプリケートされたドメイン コントローラーは、[テスト フェールオーバー](#test-failover-considerations)に使用します。 次の要件を満たすようにしてください。

1. ドメイン コントローラーがグローバル カタログ サーバーである
2. ドメイン コントローラーが、テスト フェールオーバー中に必要な役割に対応する FSMO 役割の所有者である (また、これらの役割はフェールオーバー後に[強制](http://aka.ms/ad_seize_fsmo)する必要があります)

### <a name="configure-virtual-machine-network-settings"></a>仮想マシンのネットワーク設定の構成
ドメイン コントローラー/DNS 仮想マシンに関して、Site Recovery のレプリケートされた仮想マシンの [コンピューティングとネットワーク] 設定でネットワーク設定を構成します。 これにより、仮想マシンがフェールオーバー後に適切なネットワークに接続されます。

## <a name="protect-active-directory-with-active-directory-replication"></a>Active Directory レプリケーションで Active Directory を保護する
### <a name="site-to-site-protection"></a>サイト間の保護
セカンダリ サイトでドメイン コントローラーを作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側で使用されているのと同じドメイン名を指定してください。 **Active Directory サイトとサービス** スナップインを使用して、サイトの追加先となるサイト リンク オブジェクトに対する設定を構成することができます。 サイト リンクで設定を構成することで、2 つまたはそれ以上のサイト間でレプリケーションを発生させる時間と頻度を制御できます。 詳細については、「[サイト間でのレプリケーションをスケジュールする](https://technet.microsoft.com/library/cc731862.aspx)」を参照してください。

### <a name="site-to-azure-protection"></a>サイトと Azure 間の保護
次の手順に従って、 [Azure 仮想ネットワークにドメイン コントローラー](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)を作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側と同じドメイン名を指定してください。

その後、Azure で DNS サーバーを使用するには、 [仮想ネットワークの DNS サーバーを再構成](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)します。

![Azure ネットワーク](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure 間の保護
次の手順に従って、 [Azure 仮想ネットワークにドメイン コントローラー](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)を作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側と同じドメイン名を指定してください。

その後、Azure で DNS サーバーを使用するには、 [仮想ネットワークの DNS サーバーを再構成](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)します。

## <a name="test-failover-considerations"></a>テスト フェールオーバーの考慮事項
テスト フェールオーバーは、運用環境のワークロードに影響がないよう、運用ネットワークから分離されたネットワークで行われます。

ほとんどのアプリケーションでは、ドメイン コントローラーと DNS サーバーが動作する必要もあります。 そのため、アプリケーションでフェールオーバーを実行する前に、テスト フェールオーバーに使用する分離されたネットワークでドメイン コントローラーを作成する必要があります。 これを行う最も簡単な方法は、Site Recovery によるドメイン コントローラー/DNS 仮想マシンのレプリケートです。 次に、アプリケーションの復旧計画のテスト フェールオーバーを実行する前に、ドメイン コントローラー仮想マシンのテスト フェールオーバーを実行します。 その方法は次のとおりです。

1. Site Recovery を使用して、ドメイン コントローラー/DNS 仮想マシンを[レプリケート](site-recovery-replicate-vmware-to-azure.md)します。
2. 分離されたネットワークを作成します。 既定では、Azure で作成されるすべての仮想ネットワークは、その他のネットワークから分離します。 このネットワークの IP アドレス範囲は、運用ネットワークと同じものを使用することをお勧めします。 このネットワーク上でサイト間接続を有効化しないでください。
3. DNS 仮想マシンに取得させる IP アドレスとして、作成したネットワークに DNS の IP アドレスを指定します。 レプリケート先として Azure を使っている場合、レプリケートされる仮想マシンの **[コンピューティングとネットワーク]** 設定の **[ターゲット IP]** 設定で、フェールオーバーで使われる VM の IP アドレスを指定します。

    ![Azure テスト ネットワーク](./media/site-recovery-active-directory/azure-test-network.png)

> [!TIP]
> Site Recovery は、仮想マシンの **[コンピューティングとネットワーク]** の設定で提供されたものと同じ名前のサブネットに、同じ IP を使って、テスト仮想マシンの作成を試みます。 テスト フェールオーバー用に提供された Azure 仮想ネットワークで同じ名前のサブネットを使用できない場合は、アルファベット順で最初のサブネットにテスト仮想マシンが作成されます。 ターゲット IP が選択したサブネットの一部である場合、Site Recovery は、ターゲット IP を使用してテスト フェールオーバー仮想マシンの作成を試みます。 ターゲット IP が選択したサブネットの一部でない場合、テスト フェールオーバー仮想マシンは選択したサブネット内の使用可能な次の IP を使用して作成されます。
>
>


1. 別のオンプレミス サイトにレプリケートしていて、かつ DHCP を使用している場合は、所定の手順に従って、[テスト フェールオーバー用に DNS と DHCP をセットアップ](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)してください。
2. 分離されたネットワークで実行されているドメイン コントローラー仮想マシンのテスト フェールオーバーを行います。 テスト フェールオーバーの実行には、ドメイン コントローラー仮想マシンにある**アプリケーション整合性**復旧ポイントのうち最新のものを使用します。
3. アプリケーションの仮想マシンが含まれている復旧計画のテスト フェールオーバーを実行します。
4. テストが完了したら、ドメイン コントローラー仮想マシンの**テスト フェールオーバーをクリーンアップ**します。 この手順によって、テスト フェールオーバーのために作成されたドメイン コントローラーが削除されます。


### <a name="removing-reference-to-other-domain-controllers"></a>その他のドメイン コントローラーへの参照を削除する
テスト フェールオーバーを実行する場合、テスト用ネットワークですべてのドメイン コントローラーを使用するわけではありません。 運用環境に存在する他のドメイン コントローラーの参照を削除するには、場合によっては使用しないドメイン コントローラーに [Active Directory の FSMO 役割を強制](http://aka.ms/ad_seize_fsmo)し、[メタデータのクリーンアップ](https://technet.microsoft.com/library/cc816907.aspx)を実行する必要があります。



> [!IMPORTANT]
> 次のセクションで説明する構成の一部は、標準/既定のドメイン コントローラー構成ではありません。 運用環境のドメイン コントローラーにこれらの変更を加えたくない場合は、Site Recovery のテスト フェールオーバー専用のドメイン コントローラーを作成し、それに変更を加えてもかまいません。  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>仮想化のセーフガードが原因で発生する問題

Windows Server 2012 以降では、[Active Directory Domain Services に追加のセーフガードが組み込まれています](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)。 土台となるハイパーバイザー プラットフォームが VM-GenerationID に対応していれば、USN ロールバックから仮想ドメイン コントローラーを保護することが可能です。 Azure は VM-GenerationID に対応しています。つまり、Azure 仮想マシン上で Windows Server 2012 以降を実行しているドメイン コントローラーには、その特別な保護機能が備わっています。


VM-GenerationID がリセットされると、AD DS データベースの invocationID もリセットされ、RID プールは破棄され、SYSVOL は non-authoritative とマークされます。 詳細については、[Active Directory Domain Services の仮想化の概要](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)に関するページと「[Safely Virtualizing DFSR (DFSR の安全な仮想化)](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)」を参照してください。

Azure へのフェールオーバーを行うと、VM-GenerationID がリセットされることがあります。このリセットが実行されると、Azure でのドメイン コントローラー仮想マシンの起動時に追加のセーフガードが作動します。 これが原因で、ユーザーがドメイン コントローラー仮想マシンにログインできるまでに**かなり時間がかかる**可能性があります。 このドメイン コントローラーはテスト フェールオーバー専用であるため、仮想化のセーフガードは必要ありません。 ドメイン コントローラー仮想マシンの VM-GenerationID が変更されないようにするには、オンプレミスのドメイン コントローラーで次の DWORD の値を 4 に変更します。


        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start


#### <a name="symptoms-of-virtualization-safeguards"></a>仮想化のセーフガードの兆候

テスト フェールオーバー後に仮想化のセーフガードが動作した場合、次の兆候が 1 つ以上見られます。  

生成 ID の変更

![生成 ID の変更](./media/site-recovery-active-directory/Event2170.png)

呼び出し ID の変更

![呼び出し ID の変更](./media/site-recovery-active-directory/Event1109.png)

Sysvol 共有と Netlogon 共有が使用不可

![Sysvol 共有](./media/site-recovery-active-directory/sysvolshare.png)

![Ntfrs Sysvol](./media/site-recovery-active-directory/Event13565.png)

DFSR データベースが削除

![DFSR DB の削除](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> 次のセクションで説明する構成の一部は、標準/既定のドメイン コントローラー構成ではありません。 運用環境のドメイン コントローラーにこれらの変更を加えたくない場合は、Site Recovery のテスト フェールオーバー専用のドメイン コントローラーを作成し、それに変更を加えてもかまいません。  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>テスト フェールオーバーの間のドメイン コントローラーの問題のトラブルシューティング


コマンド プロンプトで次のコマンドを実行して、SYSVOL フォルダーと NETLOGON フォルダーが共有されているかどうかを確認します。

    NET SHARE

コマンド プロンプトで次のコマンドを実行して、ドメイン コントローラーが正常に機能していることを確認します。

    dcdiag /v > dcdiag.txt

出力ログで次のテキストを探し、ドメイン コントローラーが正しく機能していることを確認します。

* "passed test Connectivity"
* "passed test Advertising"
* "passed test MachineAccount"

上記の条件が満たされている場合、ドメイン コントローラーは正常に機能していると考えられます。 条件が満たされていない場合、次の手順を試してみてください。


* ドメイン コントローラーの Authoritative Restore を実行します。
    * [FRS レプリケーションを使うことは推奨されません](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)が、まだ使っている場合は、[こちら](https://support.microsoft.com/kb/290762)で説明されている手順に従って、Authoritative Restore を行います。 前記のリンクで言及されている Burflags について詳しくは、[こちら](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)をご覧ください。
    * DFSR レプリケーションを使っている場合は、[こちら](https://support.microsoft.com/kb/2218556)の手順に従って、Authoritative Restore を行います。 また、[こちらのリンク](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)で説明されている Powershell 関数をこの目的に使うこともできます。

* オンプレミスのドメイン コントローラーで次のレジストリ キーを 0 に設定して、初期同期要件をバイパスします。 この DWORD 値が存在しない場合は、"Parameters" ノードの下に作成できます。 詳しくは、[こちら](https://support.microsoft.com/kb/2001093)をご覧ください。

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* オンプレミスのドメイン コントローラーで次のレジストリ キーを 1 に設定して、ユーザーのログオンを検証するためにグローバル カタログ サーバーを利用する要件を無効にします。 この DWORD 値が存在しない場合は、"Lsa" ノードの下に作成できます。 詳しくは、[こちら](http://support.microsoft.com/kb/241789)をご覧ください。

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS とドメイン コントローラーが異なるマシン上に存在する場合
DNS がドメイン コントローラーと同じ仮想マシン上にない場合、テスト フェールオーバー用の DNS VM を作成する必要があります。 両者が同じ VM 上にある場合は、このセクションを省略できます。

新規の DNS サーバーを使用し、必要なすべてのゾーンを作成することができます。 たとえば、Active Directory ドメインが contoso.com である場合には、contoso.com という名前で DNS ゾーンを作成することができます。Active Directory に対応するエントリは DNS で次のように更新する必要があります。

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

## <a name="next-steps"></a>次の手順
Azure Site Recovery を使用したエンタープライズ ワークロードの保護について、[こちら](site-recovery-workload.md)を参照してください。
