---
title: Azure NetApp Files についてよく寄せられる質問 | Microsoft Docs
description: ネットワーク、セキュリティ、パフォーマンス、容量管理、データ移行/保護など、Azure NetApp Files についてよく寄せられる質問を確認します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: b-juche
ms.openlocfilehash: 119cf21f90102f7ebccd8e4e06cd5e5dee3c4bfe
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427986"
---
# <a name="faqs-about-azure-netapp-files"></a>Azure NetApp Files についての FAQ

この記事では、Azure NetApp Files についてよく寄せられる質問とその回答を紹介します。 

## <a name="networking-faqs"></a>ネットワークに関する FAQ

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>NFS または SMB 用のデータ パスはインターネットを経由しますか?  

いいえ。 NFS または SMB 用のデータ パスはインターネットを経由しません。 Azure NetApp Files は、サービスが使用可能な Azure Virtual Network (VNet) にデプロイされている Azure のネイティブ サービスです。 Azure NetApp Files は、委任されたサブネットを使用し、VNet 上に直接ネットワーク インターフェイスをプロビジョニングします。 

詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>既に作成済みの VNet を Azure NetApp Files サービスに接続できますか?

はい。作成済みの VNet をサービスに接続することができます。 

詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 名を使用して Azure NetApp Files の NFS ボリュームをマウントできますか?

はい。必要な DNS エントリを作成する場合は、できます。 Azure NetApp Files には、プロビジョニングされたボリューム用の サービス IP が用意されています。 

> [!NOTE] 
> Azure NetApp Files は、必要に応じてサービスの追加の IP をデプロイできます。  DNS エントリの定期的な更新が必要になる場合があります。

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームの独自の IP アドレスを設定または選択できますか?  

いいえ。 Azure NetApp Files ボリュームへの IP 割り当ては動的です。 静的 IP の割り当てはサポートされていません。 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files はデュアル スタック (IPv4 および IPv6) VNet をサポートしていますか?

いいえ。Azure NetApp Files は現時点ではデュアル スタック (IPv4 および IPv6) VNet をサポートしていません。  

### <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>ゲスト OS マウントのために Azure VMWare ソリューションを使用する IP アドレスの数は [1,000 に制限されていますか](azure-netapp-files-resource-limits.md#resource-limits)?

いいえ。 Azure VMWare ソリューションは ER ゲートウェイの内側にあるため、オンプレミスのシステムと同様の動作をします。 AVS の "ホスト" と "ゲスト" の数は Azure NetApp Files には見えないため、IP アドレスの 1,000 件の制限は適用されません。
 
## <a name="security-faqs"></a>セキュリティに関する FAQ

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM とストレージ間のネットワーク トラフィックは暗号化できますか?

Azure NetApp Files のデータ トラフィックは、パブリック エンドポイントが提供されず、データ トラフィックは顧客所有の VNet 内に留まっているので、設計上、本質的にセキュリティが確保されています。 既定では、転送中のデータは暗号化されません。 ただし、(NFS または SMB クライアントを実行している) Azure VM から Azure NetApp Files へのデータ トラフィックは、Azure VM 間のトラフィックと同様に安全です。 

NFSv3 プロトコルでは暗号化がサポートされていないため、この転送中のデータを暗号化することはできません。 ただし、NFSv4.1 と SMB3 による転送中のデータの暗号化は、必要に応じて有効にすることができます。 NFSv4.1 クライアントと Azure NetApp Files ボリューム間のデータ トラフィックは、AES-256 暗号化を使用した Kerberos を使用して暗号化できます。 詳細については、「[Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)」を参照してください。 SMB3 クライアントと Azure NetApp Files ボリュームの間のデータ トラフィックは、SMB 3.0 では AES-CCM アルゴリズムを使用して、SMB 3.1.1 接続では AES-GCM アルゴリズムを使用して、暗号化できます。 詳細については、「[Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)」を参照してください。 

### <a name="can-the-storage-be-encrypted-at-rest"></a>ストレージは保存時に暗号化できますか?

Azure NetApp Files のすべてのボリュームは、FIPS 140-2 標準を使用して暗号化されます。 すべてのキーは Azure NetApp Files サービスによって管理されています。 

### <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Azure NetApp Files のリージョン間レプリケーションのトラフィックは暗号化されますか?

Azure NetApp Files のリージョン間レプリケーションでは、TLS 1.2 AES-256 GCM 暗号化を使用して、ソース ボリュームと宛先ボリュームの間で転送されるすべてのデータを暗号化します。 この暗号化は、Azure NetApp Files のリージョン間レプリケーションを含むすべての Azure トラフィックに対して既定で有効になっている [Azure MACSec 暗号化](../security/fundamentals/encryption-overview.md)に加えて行われます。 

### <a name="how-are-encryption-keys-managed"></a>暗号化キーはどのように管理されるのですか? 

Azure NetApp Files のキー管理は、サービスによって処理されます。 ボリュームごとに一意の XTS-AES-256 データ暗号化キーが生成されます。 すべてのボリューム キーは、暗号化キーの階層を使用して暗号化され、保護されます。 これらの暗号化キーは決して表示されず、また、暗号化されていない形式でレポートされることもありません。 ボリュームが削除されると暗号化キーは直ちに削除されます。

Azure Dedicated HSM を使用したカスタマー マネージド キー (Bring Your Own Key) のサポートは、制御ベースで、米国東部、米国中南部、米国西部 2、および US Gov バージニアの各リージョンで利用できます。 [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) にアクセスを要求することもできます。 容量が利用可能になると、要求が承認されます。

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>NFS エクスポート ポリシー規則を、Azure NetApp Files サービスのマウント ターゲットへのアクセスを制御するように構成できますか?

はい。1 つの NFS エクスポート ポリシーで、最大 5 つの規則を構成できます。

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Azure NetApp Files は、ネットワーク セキュリティ グループをサポートしていますか?

いいえ。現在のところ、ネットワーク セキュリティ グループは、Azure NetApp Files の委任されたサブネットまたはサービスによって作成されたネットワーク インターフェイスに適用することはできません。

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Azure NetApp Files で Azure RBAC を使用できますか?

はい。Azure NetApp Files では、Azure RBAC の機能がサポートされています。 組み込みの Azure ロールに加え、Azure NetApp Files 用に[カスタム ロールを作成](../role-based-access-control/custom-roles.md)できます。 

Azure NetApp Files のアクセス許可の詳細な一覧については、「Azure リソース プロバイダーの操作」で「[`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp)」を参照してください。

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Azure NetApp Files で Azure アクティビティ ログはサポートされていますか?

Azure NetApp Files は Azure のネイティブ サービスです。 Azure NetApp Files に対する PUT、POST、および DELETE API がすべてログに記録されます。 たとえば、スナップショットを作成したユーザー、ボリュームを変更したユーザーといったアクティビティがログに表示されます。

API 操作の詳細な一覧については、「[Azure NetApp Files REST API](/rest/api/netapp/)」を参照してください。

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Azure NetApp Files で Azure のポリシーを使用できますか?

はい、[カスタム Azure ポリシー](../governance/policy/tutorials/create-custom-policy-definition.md)を作成することができます。 

ただし、Azure NetApp Files インターフェイスに Azure ポリシー (カスタム名前付けポリシー) を作成することはできません。 「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md#considerations)」を参照してください。

### <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Azure NetApp Files のボリュームを削除するとき、データは安全に削除されますか? 

Azure NetApp Files ボリュームの削除は、プログラムによってただちに実行されます。 削除操作には、保存データの暗号化に使用されるキーの削除が含まれます。 削除操作が正常に実行された後では、(Azure portal や API などのインターフェイスを使用して) 削除されたボリュームを回復するためのシナリオはありません。

## <a name="performance-faqs"></a>パフォーマンスに関する FAQ

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files のパフォーマンスを最適化したり調整したりするには、どうすればよいですか?

パフォーマンス要件に従って、次の操作を実行できます。 
- 仮想マシンのサイズが適切であることを示します。
- VM の高速ネットワークを有効にします。
- 容量プールに使用したいサービス レベルとサイズを選択します。
- 容量とパフォーマンスに使用したいクォータ サイズのボリュームを作成します。

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files のスループット ベースのサービス レベルを IOPS に変換するには、どうすればよいですか?

次の数式を使用して、MB/秒を IOPS に変換できます。  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>ボリュームのサービス レベルを変更するにはどうすればよいですか?

既存のボリュームのサービス レベルは変更できます。それには、目的のボリュームで使用したい[サービス レベル](azure-netapp-files-service-levels.md)を使用中の別の容量プールに、目的のボリュームを移動します。 「[ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md)」を参照してください。 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp Files のパフォーマンスを監視するには、どうすればよいですか?

Azure NetApp Files には、ボリュームのパフォーマンス メトリックが用意されています。 また、Azure Monitor を使用して Azure NetApp Files の使用状況のメトリックを監視することもできます。  Azure NetApp Files のパフォーマンス メトリックの一覧については、「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv4.1 での Kerberos のパフォーマンスへの影響はどのようなものですか?

NFSv 4.1 のセキュリティ オプション、テスト済みのパフォーマンス ベクトル、予想されるパフォーマンスへの影響については、[NFSv 4.1 ボリュームでの Kerberos のパフォーマンスへの影響](performance-impact-kerberos.md)に関するページを参照してください。 

### <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files では SMB ダイレクトはサポートされていますか?

いいえ。Azure NetApp Files では SMB ダイレクトはサポートされていません。 

### <a name="is-nic-teaming-supported-in-azure"></a>Azure では NIC チーミングはサポートされていますか?

NIC チーミングは Azure ではサポートされていません。 Azure 仮想マシンでは複数のネットワーク インターフェイスがサポートされていますが、それらは物理的な構成ではなく論理的な構成を表します。 そのため、フォールト トレランスは提供されません。  また、Azure 仮想マシンで利用できる帯域幅は、個々のネットワーク インターフェイスではなく、マシン自体に対して計算されます。

### <a name="are-jumbo-frames-supported"></a>ジャンボ フレームはサポートされていますか?

Azure 仮想マシンでは、ジャンボ フレームはサポートされていません。

## <a name="nfs-faqs"></a>NFS に関する FAQ

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM が起動または再起動されたときに、ボリュームが自動的にマウントされるようにしたいと考えています。  NFS の永続ボリュームのホストを構成するにはどうすればよいですか?

VM の起動または再起動時に NFS ボリュームを自動的にマウントする場合は、ホスト上の `/etc/fstab` ファイルにエントリを追加します。 

詳細については、「[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)」を参照してください。  

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files でサポートされている NFS のバージョンを何ですか?

Azure NetApp Files では、NFSv3 および NFSv4.1 がサポートされています。 いずれかの NFS バージョンを使用して[ボリュームを作成](azure-netapp-files-create-volumes.md)できます。 

### <a name="how-do-i-enable-root-squashing"></a>ルート スカッシュを有効にするにはどうすればよいですか?

ボリュームのエクスポート ポリシーを使用して、ルート アカウントがボリュームにアクセスできるかどうかを指定できます。 詳細については、「[NFS ボリュームのエクスポート ポリシーを構成する](azure-netapp-files-configure-export-policy.md)」を参照してください。

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>複数のボリュームに対して同じファイル パス (ボリューム作成トークン) を使用することはできますか?

はい、できます。 ただし、ファイル パスは、別のサブスクリプションまたは別のリージョンのいずれかで使用されているものでなくてはなりません。   

たとえば、`vol1` というボリュームを作成するとします。 次に、別の容量プールで、ただし同じサブスクリプションとリージョン内で、同じく `vol1` と呼ばれる別のボリュームを作成します。 この場合は、同じボリューム名 `vol1` を使用すると、エラーが発生します。 同じファイル パスを使用するには、名前が別のリージョンまたはサブスクリプションに存在している必要があります。

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Windows クライアントを使用して NFS ボリュームにアクセスしようとすると、クライアントによるフォルダーやサブフォルダーの検索に時間がかかるのはなぜですか?

フォルダーとサブフォルダーの検索を高速化するために、Windows クライアントで `CaseSensitiveLookup` が有効になっていることを確認します。

1. 次の PowerShell コマンドを使用して CaseSensitiveLookup を有効にします。   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Windows サーバーでそのボリュームをマウントします。   
    例:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp Files では NFSv4.1 のファイル ロックはどのようにサポートされますか? 

NFSv 4.1 クライアントの場合、Azure NetApp Files では、リース ベースのモデルですべてのファイル ロックの状態を維持する NFSv4.1 ファイル ロック メカニズムをサポートします。 

RFC 3530 に基づき、Azure NetApp Files では、NFS クライアントによって保持されるすべての状態に対して 1 つのリース期間を定義します。 定義された期間内にクライアントによってリースが更新されない場合、クライアントのリースに関連付けられているすべての状態がサーバーによって解放されます。  

たとえば、ボリュームをマウントするクライアントがタイムアウトを超えて応答しなくなったりクラッシュしたりすると、ロックが解放されます。 クライアントでは、ファイルの読み取りなどの操作を実行することで、明示的または暗黙的にリースを更新できます。   

猶予期間とは、クライアントがサーバーの回復中にロック状態を回収できるようにする特別な処理の期間を定義するものです。 リースの既定のタイムアウトは 30 秒で、猶予期間は 45 秒です。 その期間が経過すると、クライアントのリースが解放されます。   

## <a name="smb-faqs"></a>SMB に関する FAQ

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Azure NetApp Files でサポートされている SMB のバージョンは何ですか?

Azure NetApp Files では、SMB 2.1 と SMB 3.1 (SMB 3.0 のサポートを含む) がサポートされています。    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>SMB でのアクセスに Active Directory 接続は必要ですか? 

はい。SMB ボリュームをデプロイする前に Active Directory の接続を作成する必要があります。 指定したドメイン コントローラーは、正常に接続するためには、Azure NetApp Files の委任されたサブネットからアクセス可能である必要があります。  詳細については、「[SMB ボリュームを作成する](./azure-netapp-files-create-volumes-smb.md)」を参照してください。 

### <a name="how-many-active-directory-connections-are-supported"></a>サポートされている Active Directory 接続はいくつですか?

Active Directory (AD) 接続を構成できるのは、サブスクリプションごとおよびリージョンごとに 1 つだけです。 詳細については、「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を参照してください。 

ただし、同じサブスクリプションと同じリージョンにある複数の NetApp アカウントを、いずれかの NetApp アカウントで作成された共通の AD サーバーにマップすることができます。 「[同じサブスクリプションとリージョンにある複数の NetApp アカウントを AD 接続にマップする](create-active-directory-connections.md#shared_ad)」を参照してください。 

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp Files で Azure Active Directory はサポートされていますか? 

[Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) と [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) の両方がサポートされます。 Azure NetApp Files で既存の Active Directory ドメイン コントローラーを使用することができます。 ドメイン コントローラーは、仮想マシンとして Azure に配置することも、ExpressRoute またはサイト間 VPN 経由でオンプレミスに配置することもできます。 Azure NetApp Files は現時点で、AD join for [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) をサポートしていません。

Azure Active Directory Domain Services と組み合わせて Azure NetApp Files を使用している場合、NetApp アカウント用に Active Directory を構成する際の組織単位のパスは `OU=AADDC Computers` になります。

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>サポートされる Windows Server Active Directory のバージョンを教えてください。

Azure NetApp Files では、Windows Server 2008r2SP1-2019 バージョンの Active Directory Domain Services がサポートされています。

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>SMB 共有に接続すると問題が発生します。 どうすればよいですか。

ベスト プラクティスとして、コンピューターのクロック同期の最大許容値を 5 分に設定します。 詳細については、[コンピューターのクロック同期の最大許容値](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11))に関するページをご覧ください。 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>コンピューターの管理コンソール (MMC) を使用して、`SMB Shares`、`Sessions`、および `Open Files` を管理できますか?

コンピューターの管理コンソール (MMC) を使用した `SMB Shares`、`Sessions`、および `Open Files` の管理は、現在サポートされていません。

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>ポータルを使用して SMB ボリュームの IP アドレスを取得するにはどうすればよいですか?

ボリュームの概要ペインの **[JSON ビュー]** リンクを使用して、 **[プロパティ]**  ->  **[mountTargets]** の下で **[startIp]** 識別子を探します。

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Azure NetApp Files SMB 共有を、DFS 名前空間 (DFS-N) のルートとして使用できますか。

いいえ。 ただし、Azure NetApp Files SMB 共有は、DFS 名前空間 (DFS-N) のフォルダー ターゲットとして使用できます。   
Azure NetApp Files SMB 共有を DFS-N のフォルダー ターゲットとして使用するには、[DFS のフォルダー ターゲット追加](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target)に関する記事の手順に従って、Azure NetApp Files SMB 共有のマウント ポイントの汎用名前付け規則 (UNC) パスを設定します。  

### <a name="can-the-smb-share-permissions-be-changed"></a>SMB の共有アクセス許可を変更することはできますか?   

いいえ、共有アクセス許可を変更することはできません。 ただし、`root` ボリュームの NTFS アクセス許可は、[NTFS ファイルとフォルダーのアクセス許可](azure-netapp-files-create-volumes-smb.md#ntfs-file-and-folder-permissions)の手順で変更することができます。 

## <a name="capacity-management-faqs"></a>容量管理に関する FAQ

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Azure NetApp Files の容量プールとボリュームの使用状況を監視するには、どうすればよいですか? 

Azure NetApp Files には、容量プールとボリュームの使用状況のメトリックが用意されています。 また、Azure Monitor を使用して Azure NetApp Files の使用状況を監視することもできます。 詳細については、「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。 

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>ディレクトリがサイズ制限に近づいているかどうかを確認するにはどうすればよいですか?

ディレクトリがディレクトリ メタデータの[最大サイズの制限](azure-netapp-files-resource-limits.md#resource-limits) (320 MB) に近づいているかどうかを確認するには、クライアントから `stat` コマンドを使用できます。
上限と計算方法については「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md#directory-limit)」をご覧ください。 

<!-- 
You can use the `stat` command from a client to see whether a directory is approaching the maximum size limit for directory metadata (320 MB).   

For a 320-MB directory, the number of blocks is 655360, with each block size being 512 bytes.  (That is, 320x1024x1024/512.)  This number translates to approximately 4 million files maximum for a 320-MB directory. However, the actual number of maximum files might be lower, depending on factors such as the number of files containing non-ASCII characters in the directory. As such, you should use the `stat` command as follows to determine whether your directory is approaching its limit.  

Examples:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```
--> 

### <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>スナップショット領域は、ボリュームの使用可能な容量またはプロビジョニングされた容量にカウントされますか?

はい。[消費されたスナップショット容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)は、ボリューム内のプロビジョニングされた領域にカウントされます。 ボリュームがいっぱいになった場合は、次のアクションを実行することを検討してください。

* [ボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)。
* ホストしているボリューム内の領域を解放するために、[以前のスナップショットを削除する](azure-netapp-files-manage-snapshots.md#delete-snapshots)。 

### <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Azure NetApp Files では、ボリュームまたは容量プールの自動拡張をサポートしていますか?

いいえ。Azure NetApp Files ボリュームおよび容量プールは、いっぱいになっても自動拡張されません。 「[Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)」を参照してください。   

容量ベースのアラート ルールを管理するには、コミュニティでサポートされている [Logic Apps ANFCapacityManager ツール](https://github.com/ANFTechTeam/ANFCapacityManager)を使用できます。 このツールでは、ボリュームの領域が不足しないように、ボリューム サイズを自動的に増やすことができます。

### <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>レプリケーションの宛先ボリュームは、ハード ボリューム クォータにカウントされますか?  

いいえ。レプリケーションの宛先ボリュームは、ハード ボリューム クォータにカウントされません。

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure NetApp Files は Azure Storage Explorer を使用して管理できますか?

いいえ。 Azure NetApp Files は、Azure Storage Explorer ではサポートされていません。

## <a name="data-migration-and-protection-faqs"></a>データの移行と保護に関する FAQ

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Azure NetApp Files にデータを移行するにはどうすればよいですか?
Azure NetApp Files には NFS ボリュームと SMB ボリュームがあります。  ファイル ベースのコピー ツールを使用すると、データをサービスに移行することができます。 

NetApp には、SaaS ベースのソリューションである [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service) があります。このソリューションでは、NFS または SMB のデータを、Azure NetApp Files の NFS エクスポートまたは SMB 共有にレプリケートすることができます。 

また、さまざまな無料のツールを使用してデータをコピーすることもできます。 NFS の場合は、[rsync](https://rsync.samba.org/examples.html) などのワークロード ツールを使用して、Azure NetApp Files ボリュームにソース データをコピーしたり同期したりできます。 SMB の場合は、ワークロード [robocopy](/windows-server/administration/windows-commands/robocopy) を同じ方法で使用することができます。  これらのツールでは、ファイルまたはフォルダーのアクセス許可をレプリケートすることもできます。 

オンプレミスから Azure NetApp Files へのデータ移行の要件は、次のとおりです。 

- Azure NetApp Files がターゲット Azure リージョンで利用できることを確認します。
- ソースと Azure NetApp Files のターゲット ボリュームの IP アドレス間のネットワーク接続を検証します。 オンプレミスと Azure NetApp Files サービスとの間のデータ転送は、ExpressRoute 経由でサポートされています。
- ターゲットの Azure NetApp Files ボリュームを作成します。
- 使用したいファイル コピー ツールを使用して、ターゲット ボリュームにソース データを転送します。

### <a name="where-does-azure-netapp-files-store-customer-data"></a>Azure NetApp Files では、顧客データはどこに格納されますか?   

既定では、Azure NetApp Files ボリュームをデプロイするリージョン内にデータがとどまります。 ただし、[リージョン間レプリケーション](cross-region-replication-introduction.md)を使って、使用できる宛先リージョンにデータをボリューム単位でレプリケートすることができます。

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Azure NetApp Files ボリュームのコピーを別の Azure リージョンに作成するには、どうすればよいですか?
    
Azure NetApp Files には NFS ボリュームと SMB ボリュームがあります。  ファイル ベースのコピー ツールは、Azure リージョン間でデータをレプリケートするために使用できます。 

[リージョン間レプリケーション](cross-region-replication-introduction.md)機能を使用すると、あるリージョン内の Azure NetApp Files ボリューム (ソース) から別のリージョン内の別の Azure NetApp Files ボリューム (宛先) にデータを非同期的にレプリケートできます。  さらに、[既存のボリュームのスナップショットを使用して新しいボリュームを作成](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume)できます。

NetApp には、SaaS ベースのソリューションである [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service) があります。このソリューションでは、NFS または SMB のデータを、Azure NetApp Files の NFS エクスポートまたは SMB 共有にレプリケートすることができます。 

また、さまざまな無料のツールを使用してデータをコピーすることもできます。 NFS の場合は、[rsync](https://rsync.samba.org/examples.html) などのワークロード ツールを使用して、Azure NetApp Files ボリュームにソース データをコピーしたり同期したりできます。 SMB の場合は、ワークロード [robocopy](/windows-server/administration/windows-commands/robocopy) を同じ方法で使用することができます。  これらのツールでは、ファイルまたはフォルダーのアクセス許可をレプリケートすることもできます。 

Azure NetApp Files ボリュームを別の Azure リージョンにレプリケートするための要件は次のとおりです。 
- Azure NetApp Files がターゲット Azure リージョンで利用できることを確認します。
- 各リージョン内の Vnet 間のネットワーク接続を検証します。 現在のところ、VNet 間のグローバル ピアリングはサポートされていません。  VNet 間の接続は、ExpressRoute 回線とリンクするか、S2S VPN 接続を使用することで確立することができます。 
- ターゲットの Azure NetApp Files ボリュームを作成します。
- 使用したいファイル コピー ツールを使用して、ターゲット ボリュームにソース データを転送します。

### <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box を使用した移行はサポートされていますか?

いいえ。 Azure Data Box では、現在のところ Azure NetApp Files はサポートされていません。 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Import/Export サービスを使用した移行はサポートされていますか?

いいえ。 Azure Import/Export サービスでは、現在のところ Azure NetApp Files はサポートされません。

## <a name="product-faqs"></a>製品に関する FAQ

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) で Azure NetApp Files NFS または SMB ボリュームを使用できますか?

Azure NetApp Files NFS ボリュームは、AVS Windows VM または Linux VM にマウントできます。 AVS Windows VM 上で Azure NetApp Files SMB 共有をマップできます。 詳細については、「[Azure NetApp Files と Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)」を参照してください。  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) で Azure NetApp Files NFS または SMB ボリュームを使用する場合、どのリージョンがサポートされますか?

AVS での Azure NetApp Files の NFS または SMB ボリュームを使用した *ゲスト OS のマウント* は、[AVS および ANF が有効なすべてのリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp)でサポートされています。

### <a name="does-azure-netapp-files-work-with-azure-policy"></a>Azure NetApp Files で Azure Policy を使用できますか。

はい。 Azure NetApp Files はファーストパーティーのサービスです。 Azure リソース プロバイダーの標準に完全に準拠しています。 そのため Azure NetApp Files は、*カスタム ポリシー定義* によって Azure Policy に統合できます。 Azure NetApp Files でカスタム ポリシーを使用する方法は、Microsoft Tech Community の [Azure NetApp Files での Azure Policy の使用](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258)に関するページをご覧ください。 

### <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>ファイル名とディレクトリ名の作成と表示に、Azure NetApp Files でサポートされている Unicode 文字エンコードは何ですか?   

Azure NetApp Files で、NFS と SMB の両方のボリュームのファイル名とディレクトリ名についてサポートされているのは、UTF-8 Unicode 文字エンコード形式のみです。

UTF-8 によってサポートされていない非標準文字や絵文字などの補助文字やサロゲート ペアを名前に使用して、ファイルまたはディレクトリを作成しようとすると、操作は失敗します。 この場合、Windows クライアントからのエラーは、次のようなものになります: "指定したファイル名が有効ではないか長すぎます。 別のファイル名を指定してください。" 

## <a name="next-steps"></a>次のステップ  

- [Microsoft Azure ExpressRoute に関する FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network に関する FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Azure NetApp Files の SMB パフォーマンスに関する FAQ](azure-netapp-files-smb-performance.md)
