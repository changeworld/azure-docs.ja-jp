---
title: "Azure Site Recovery を使用してファイル サーバーを保護する"
description: "この記事では、Azure Site Recovery を使用してファイル サーバーを保護する方法について説明します。"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: a746ace47c4f1190b7a695014543670a1a9cf879
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Azure Site Recovery を使用してファイル サーバーを保護する 

[Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、さまざまなワークロードのレプリケーション、フェールオーバー、復旧など、オンプレミス マシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

この記事では、Azure Site Recovery およびさまざまな環境に合わせたその他の推奨事項を使用してファイル サーバーを保護する方法について説明します。 

- [Azure IaaS ファイル サーバー マシンの保護](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [オンプレミスのファイル サーバーの保護](#replicate-an-onpremises-file-server-using-azure-site-recovery)


## <a name="file-server-architecture"></a>ファイル サーバーのアーキテクチャ
オープンな分散ファイル共有システムの目的は、地理的に分散したユーザーのグループが共同作業でファイルを効率的に使用できる環境を提供し、整合性の要件を確実に適用することです。 多数の同時ユーザーと大量のコンテンツ項目をサポートする一般的なオンプレミスのファイル サーバー エコシステムでは、レピュテーションのスケジュール設定と帯域幅の調整に分散ファイル システム レプリケーション (DFSR) を使用します。 DFSR では、Remote Differential Compression (RDC) という圧縮アルゴリズムを使用します。このアルゴリズムによって、帯域幅が制限されたネットワークでファイルを効率的に更新できます。 また、ファイル内のデータの挿入、削除、および再配置を検出し、ファイルの更新時に、変更されたファイル ブロックのみを DFSR でレプリケートできるようにします。 ピーク時以外のタイミングで毎日バックアップが作成されるファイル サーバー環境もあります。これは災害時のニーズに対応したものであり、DFSR は実装されていません。

次のトポロジは、DFSR が実装されたファイル サーバー環境を示しています。
                
![DFSR のアーキテクチャ](media/site-recovery-file-server/dfsr-architecture.JPG)

上記のリファレンスでは、メンバーと呼ばれる複数のファイル サーバーが、レプリケーション グループ全体でのファイルのレプリケートにアクティブに参加します。 レプリケートされたフォルダー内のコンテンツは、どちらかのメンバーがオフラインになった場合でも、一方のメンバーに要求を送信するすべてのクライアントが使用できます。

## <a name="disaster-recovery-recommendation-for-file-servers"></a>ファイル サーバーのディザスター リカバリーの推奨事項

1.  Azure Site Recovery を使用したファイル サーバーのレプリケート: Azure Site Recovery を使用してファイル サーバーを Azure にレプリケートできます。 オンプレミスの 1 つ以上のファイル サーバーにアクセスできない場合は、復旧 VM を Azure で起動できます。これにより、Azure でサイト間 VPN 接続と Active Directory が構成されていれば、クライアントからの要求をオンプレミスで処理できます。 これは、DFSR が構成された環境でも DFSR を使用しないシンプルなファイル サーバー環境でも可能です。 

2.  Azure Iaas VM への DFSR の拡張: DFSR を実装し、クラスター化されたファイル サーバー環境で推奨される方法の 1 つは、オンプレミスの DFSR を Azure に拡張することです。 その後で、ファイル サーバー ロールを実行するための Azure 仮想マシンが有効になります。 

    サイト間 VPN 接続と Active Directory の依存関係が処理され、DFSR の準備が整うと、オンプレミスの 1 つ以上のファイル サーバーにアクセスできなくても、クライアントは引き続き Azure VM に接続して要求を処理できます。

    VM の構成が Azure Site Recovery でサポートされていない (ファイル サーバー環境でよく使用する共有クラスター ディスクなど) 場合は、この方法を推奨します。  DFSR は、チャーン レートが中程度の低帯域幅の環境でも正しく機能します。 そのためには、Azure VM を常時稼働させておくための追加コストも必要です。  

3.  Azure File Sync サービスを使用したファイルのレプリケート: クラウドへの移行準備をしている場合や、Azure VM を既に使用している場合は、Azure File Sync サービスを使用することをお勧めします。このサービスは、業界標準の[サーバー メッセージ ブロック](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB) プロトコルを使用してアクセス可能な、クラウド内で完全に管理されたファイル共有の同期を提供します。 Azure ファイル共有は、クラウドまたはオンプレミスにある Windows、Linux、および macOS に同時にマウントできます。 

ファイル サーバー環境で使用する戦略の決定を簡略化することを目的とした図を以下に示します。

![デシジョン ツリー](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-disaster-recovery-decision"></a>ディザスター リカバリーに関する意思決定の際に考慮すべき要素

|環境  |推奨  |考慮すべき点 |
|---------|---------|---------|
|ファイル サーバー環境 (DFSR あり/なし)|   [Azure Site Recovery を使用したレプリケーション](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Site Recovery は共有ディスク クラスター (NAS) をサポートしません。 このどちらかの構成を環境で使用する場合は、その他の任意の方法を適宜使用してください。 <br> Azure Site Recovery は SMB 3.0 をサポートしません。つまり、ファイルに対する変更がファイルの元の場所で更新された場合にのみ、レプリケートされた VM がその変更を反映します。
|ファイル サーバー環境 (DFSR あり)     |  [Azure IaaS 仮想マシンへの DFSR の拡張](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR は、帯域幅が極端に不足している環境でも正しく機能します。ただし、この方法では Azure VM を常時稼働させておく必要があります。 VM のコストを計画に含めてください。         |
|Azure Iaas VM     |     [Azure File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     DR のシナリオでは、Azure File Sync を使用する場合、フェールオーバーの際に手動のアクションを実行して、クライアント マシンに対して透過的な方法でファイル共有をアクセス可能な状態にしておく必要があります。 AFS では、クライアント マシンからポート 445 を開いておく必要があります。     |


### <a name="site-recovery-support"></a>Site Recovery のサポート
Site Recovery レプリケーションはアプリケーションに依存しないため、ここで紹介する推奨事項は次のシナリオに適用できます。
| ソース    |セカンダリ サイトへ    |Azure へ
|---------|---------|---------|
|Azure| -|あり|
|Hyper-V|   あり |あり
|VMware |あり|   あり
|物理サーバー|   あり |あり
 

> [!IMPORTANT]
> 以下に示す 3 つのうちのどの方法を使用する場合でも、次の依存関係について考慮してください。

**サイト間接続**: サーバー間の通信を許可するには、オンプレミスのサイトと Azure ネットワーク間の直接接続を確立する必要があります。  そのためには、DR サイトとして使用する Microsoft Azure Virtual Network へのセキュリティで保護されたサイト間 VPN 接続が必要です。  
参照先: [オンプレミスのサイトと Azure ネットワーク間のサイト間 VPN 接続の確立](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)に関する記事

**Active Directory**: DFSR は Active Directory に依存します。  つまり、ローカル ドメイン コントローラーを使用する Active Directory フォレストが Azure のDR サイトに拡張されます。 DFSR を使用しない場合でも、ほとんどの組織と同様に、対象のユーザーにアクセス権を付与したり、ユーティリティのアクセス権を検証したりする必要があれば、これらの手順を実行してください。
参照先: [Azure へのオンプレミス Active Directory の拡張](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory)に関する記事

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS 仮想マシンのディザスター リカバリーの推奨事項

Azure IaaS VM でホストされるファイル サーバーのディザスター リカバリーを構成および管理する場合は、[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) に移動するかどうかに基づいて 2 つのオプションから選択できます。

1. [Azure File Sync の使用](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Azure Site Recovery の使用](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Azure File Sync サービスを使用して IaaS 仮想マシンでホストされるファイルをレプリケートする

**Azure Files** を使用して、従来のオンプレミス ファイル サーバーまたは NAS デバイスを完全に置き換えたり、補完することができます。 また、Azure File Sync を使用すれば、Azure のファイル共有を、オンプレミスまたはクラウドにある Windows サーバーにレプリケートすることも可能で、使用されているデータを分散キャッシュしてパフォーマンスを向上できます。 以下の手順では、従来のファイル サーバーと同じ機能を実行する Azure VM の DR の推奨事項を示します。
1.  ここで説明する手順を使用して、Azure Site Recovery でマシンを保護します。
2.  Azure File Sync を使用して、ファイル サーバーとして機能する VM からクラウドにファイルをレプリケートします。
3.  Azure Site Recovery の復旧計画機能を使用し、[Azure ファイル共有をマウント](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)して仮想マシン内の共有にアクセスするスクリプトを追加します。

以下の手順では、Azure File Sync サービスの使用方法を簡単に説明します。

1. [Azure にストレージ アカウントを作成します](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 ストレージ アカウントに対して読み取りアクセス geo 冗長ストレージ (RA-GRS) を選択した場合は、災害時にセカンダリ リージョンからデータに読み取りアクセスできます。 詳しくは、[Azure ファイル共有のディザスター リカバリーの戦略](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事をご覧ください。

2. [ファイル共有の作成](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

3. Azure ファイル サーバーに [Azure File Sync をデプロイ](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide)します。

4. 同期グループを作成します。同期グループ内のエンドポイントは、相互に同期状態が維持されます。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが少なくとも 1 つと、Windows サーバー上のパスを表すサーバー エンドポイントが 1 つ含まれている必要があります。
5.  Azure ファイル共有とオンプレミスのサーバーでファイルの同期が維持されます。
6.  オンプレミスの環境で災害が発生した場合は、復旧計画を使用してフェールオーバーを実行し、[Azure ファイル共有をマウント](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)して仮想マシン内の共有にアクセスするスクリプトを追加します。

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Azure Site Recovery を使用して IaaS ファイル サーバー仮想マシンをレプリケートする

IaaS ファイル サーバー仮想マシンにアクセスするオンプレミスのクライアントがある場合は最初の 2 つの手順も実行し、それ以外の場合は手順 3 に進みます。

1. オンプレミスのサイトと Azure ネットワーク間のサイト間 VPN 接続を確立します。  
2. オンプレミスの Active Directory を拡張します。
3. セカンダリ リージョンへの IaaS ファイル サーバー マシンの[ディザスター リカバリーを設定](azure-to-azure-tutorial-enable-replication.md)します。


セカンダリ リージョンへのディザスター リカバリーについて詳しくは、[こちら](concepts-azure-to-azure-architecture.md)をご覧ください。


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Azure Site Recovery を使用してオンプレミスのファイル サーバーをレプリケートする
以下の手順では、VMware VM のレプリケーション方法を示します。Hyper-V VM をレプリケートする手順については、こちらをご覧をください。

1.  オンプレミス マシンのレプリケーションのために [Azure リソースを準備](tutorial-prepare-azure.md)します。
2.  オンプレミスのサイトと Azure ネットワーク間のサイト間 VPN 接続を確立します。  
3. オンプレミスの Active Directory を拡張します。
4.  [オンプレミスの VMware サーバーを準備します](tutorial-prepare-on-premises-vmware.md)。
5.  Azure にオンプレミス VM の[ディザスター リカバリーを設定](tutorial-vmware-to-azure.md)します。

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Azure IaaS 仮想マシンに DFSR を拡張します。

1.  オンプレミスのサイトと Azure ネットワーク間のサイト間 VPN 接続を確立します。 
2.  オンプレミスの Active Directory を拡張します。
3.  Microsoft Azure Virtual Network で[ファイル サーバー VM を作成してプロビジョニング](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)します。

    同じ Microsoft Azure Virtual Network に仮想マシンが追加されていることを確認してください。このネットワークは、オンプレミスの環境とクロス接続されています。 

4.  Windows サーバーで DFS レプリケーションをインストールして[構成](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx)します。

5.  [DFS 名前空間を実装します](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces)。
6.  DFS 名前空間を実装すると、DFS 名前空間のフォルダー ターゲットを更新することで、運用サイトから DR サイトへの共有フォルダーのフェールオーバーを実行できます。  このような DFS 名前空間の変更が Active Directory を使用してレプリケートされると、ユーザーは適切なフォルダー ターゲットに透過的に接続されます。

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Azure File Sync サービスを使用してオンプレミス ファイルをレプリケートする
Azure File Sync サービスを使用すると、目的のファイルをクラウドにレプリケートできます。そのため、災害時およびオンプレミスのファイル サーバーが使用できない場合に、目的のファイルの場所をクラウドからマウントして、クライアント マシンから要求の処理を継続できます。
Azure File Sync と Azure Site Recovery を統合する際に推奨される方法を次に示します。
1.  [ここ](tutorial-vmware-to-azure.md)で説明する手順を使用して、Azure Site Recovery でファイル サーバー マシンを保護します。
2.  Azure File Sync を使用して、ファイル サーバーとして機能するマシンからクラウドにファイルをレプリケートします。
3.  Azure Site Recovery の復旧計画機能を使用し、Azure のフェールオーバーされたファイル サーバー VM で Azure ファイル共有をマウントするスクリプトを追加します。

以下の手順では、Azure File Sync サービスの使用方法を示します。

1. [Azure にストレージ アカウントを作成します](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 ストレージ アカウントに対して読み取りアクセス geo 冗長ストレージ (RA-GRS) (推奨) を選択した場合は、災害時にセカンダリ リージョンからデータに読み取りアクセスできます。 詳しくは、[Azure ファイル共有のディザスター リカバリーの戦略](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事をご覧ください。

2. [ファイル共有の作成](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)

3. オンプレミスのファイル サーバーに [Azure File Sync をデプロイ](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide)します。

4. 同期グループを作成します。同期グループ内のエンドポイントは、相互に同期状態が維持されます。 同期グループには、Azure ファイル共有を表すクラウド エンドポイントが少なくとも 1 つと、オンプレミスの Windows サーバー上のパスを表すサーバー エンドポイントが 1 つ含まれている必要があります。

1. Azure ファイル共有とオンプレミスのサーバーでファイルの同期が維持されます。
6.  オンプレミスの環境で災害が発生した場合は、復旧計画を使用してフェールオーバーを実行し、Azure ファイル共有をマウントして仮想マシン内の共有にアクセスするスクリプトを追加します。

> [!NOTE]
> ポート 445 が開いていること: Azure Files は SMB プロトコルを使用します。 SMB は、TCP ポート 445 経由で通信します。ファイアウォールがクライアント マシンの TCP ポート 445 をブロックしていないことを確認してください。


## <a name="doing-a-test-failover"></a>テスト フェールオーバーの実行

1.  Azure Portal に移動し、Recovery Services コンテナーを選択します。
2.  ファイル サーバー環境用に作成された復旧計画をクリックします。
3.  [テスト フェールオーバー] をクリックします。
4.  テスト フェールオーバー プロセスを開始する復旧ポイントと Azure 仮想ネットワークを選択します。
5.  セカンダリ環境が立ち上がったら、検証を行うことができます。
6.  検証が完了したら､復旧計画で [Cleanup test failover] をクリックできるようになります｡テスト フェールオーバー環境がクリーンアップされます｡

テスト フェールオーバーの実行について詳しくは、[こちら](site-recovery-test-failover-to-azure.md)をご覧ください。

AD および DNS に対するテスト フェールオーバーの実行に関するガイダンスは､[AD と DNS のテスト フェールオーバーの考慮事項](site-recovery-active-directory.md)に関する記事をご覧ください｡

## <a name="doing-a-failover"></a>フェールオーバーの実行

1.  Azure Portal に移動し､Recovery Service コンテナーを選択します｡
2.  ファイル サーバー環境用に作成された復旧計画をクリックします。
3.  [フェールオーバー] をクリックします。
4.  フェールオーバー プロセスを開始する復旧ポイントを選択します。

テスト フェールオーバーの実行について詳しくは、[こちら](site-recovery-failover.md)をご覧ください。
