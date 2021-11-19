---
title: Azure Files のネットワークに関する考慮事項 | Microsoft Docs
description: Azure Files のネットワーク オプションの概要。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d3f3bb466156b08ae90561d5e02e5b5ddf469755
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518510"
---
# <a name="azure-files-networking-considerations"></a>Azure Files のネットワークに関する考慮事項 
Azure ファイル共有には、次の 2 つの方法で接続できます。

- サーバー メッセージ ブロック (SMB)、Network File System (NFS)、または FileREST プロトコルを使用して、共有に直接アクセスします。 このアクセス パターンは主に、できるだけ多くのオンプレミス サーバーを排除するために使用されます。
- Azure File Sync を使用してオンプレミス サーバー上 (Azure VM 上) に Azure ファイル共有のキャッシュを作成し、ユース ケースに合った任意のプロトコル (SMB、NFS、FTPS など) を使用してオンプレミス サーバーからファイル共有のデータにアクセスします。 このアクセス パターンは、オンプレミスのパフォーマンスとクラウド スケールの両方、およびサーバーレスの接続可能なサービス (Azure Backup など) のメリットが組み合わされるために非常に便利です。

この記事では、Azure File Sync を使用するのではなく、直接 Azure ファイル共有にアクセスするためにユース ケースに必要となるネットワークの構成方法に重点を置いています。Azure File Sync のデプロイのネットワークに関する考慮事項の詳細については、「[Azure File Sync のネットワークに関する考慮事項](../file-sync/file-sync-networking-overview.md)」を参照してください。

Azure ファイル共有のネットワーク構成は Azure ストレージ アカウントで行います。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。 ストレージ アカウントには、ネットワーク エンドポイント、ストレージ アカウントのファイアウォール設定、転送中の暗号化など、ファイル共有へのネットワーク アクセスをセキュリティで保護するうえで役立つ多数の設定が公開されています。 

> [!Important]  
> 複数のネットワーク ルーティング オプションが、Azure Files によってサポートされています。 既定のオプションである Microsoft ルーティングは、Azure Files のすべての構成で動作します。 インターネット ルーティング オプションでは、AD ドメイン参加シナリオまたは Azure File Sync はサポートされていません。

この概念的なガイドを読む前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」を読むことをお勧めします。

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        このビデオは、簡単な 5 つのステップでインフォメーション ワーカーやアプリに直接 Azure ファイル共有を安全に公開する方法についてのガイドとデモです。 下のセクションでは、ビデオ内で参照されているドキュメントへのリンクと追加のコンテキストを提供します。
   :::column-end:::
:::row-end:::

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |

## <a name="accessing-your-azure-file-shares"></a>Azure ファイル共有へのアクセス
SMB Azure ファイル共有には、SMB 3.1.1 と SMB 3.0 を使用して、ストレージ アカウントのパブリック エンドポイントから簡単にアクセスできます。 つまり、ユーザーのログオン ID によって承認された要求など、認証済みの要求は、Azure の内外から安全に送信できます。 NFS Azure ファイル共有には、パブリック エンドポイントが Azure 仮想ネットワークに制限されている場合、ストレージ アカウントのパブリック エンドポイントからのみアクセスできます。

さまざまな環境において、Azure ファイル共有に追加のネットワーク構成を適用することが望まれる場合があります。

- SMB ファイル共有との関連で、SMB で通信に利用されるポート (ポート 445) は、多くの組織とインターネット サービス プロバイダー (ISP) によってブロックされています。 この慣例の由来は、SMB プロトコルの非推奨かつ非インターネットセーフのバージョンに関する以前のセキュリティ ガイダンスにあります。 SMB 3.x はインターネットセーフのプロトコルですが、組織や ISP のポリシーは変更できないことがあります。 

- NFS ファイル共有との関連で、制限付きパブリック エンドポイント アクセスでは、Azure 内にのみマウントが制限されます。

- 一部の組織では、Azure へのトラフィックが特定のパスを通過する必要があります。

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>仮想プライベート ネットワークまたは ExpressRoute でトラフィックをトンネリングする
オンプレミス ネットワークと Azure との間にネットワーク トンネルを確立したら、Azure 内の 1 つ以上の仮想ネットワークとオンプレミス ネットワークをピアリングします。 [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) (VNet) は、オンプレミスで運用する従来のネットワークに似ています。 Azure ストレージ アカウントや Azure VM と同様に、VNet は、リソース グループにデプロイされる Azure リソースです。 

オンプレミスのワークステーションやサーバーと Azure SMB/NFS ファイル共有の間のトラフィックをトンネリングするために、Azure Files は次のメカニズムをサポートしています。

- VPN ゲートウェイは、暗号化されたトラフィックをインターネット経由で Azure 仮想ネットワークと別の場所 (オンプレミスなど) の間で送信するために使用される特定の種類の仮想ネットワーク ゲートウェイです。 Azure VPN Gateway は、ストレージ アカウントまたはその他の Azure リソースと共にリソース グループにデプロイできる Azure リソースです。 VPN ゲートウェイは、次の 2 つの異なる種類の接続を公開します。
    - [ポイント対サイト (P2S) VPN](../../vpn-gateway/point-to-site-about.md) ゲートウェイ接続。これは、Azure と個々のクライアントの間の VPN 接続です。 このソリューションは主に、自宅、コーヒー ショップ、または出先のホテルから自分の Azure ファイル共有をマウントできるようにしたい在宅勤務者などの、組織のオンプレミス ネットワークの一部ではないデバイスに役立ちます。 Azure Files で P2S VPN 接続を使用するには、接続したいクライアントごとに P2S VPN 接続を構成する必要があります。 P2S VPN 接続のデプロイを簡略化するには、「[Windows 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する](storage-files-configure-p2s-vpn-windows.md)」および「[Linux 上で Azure Files で使用するポイント対サイト (P2S) VPN を構成する](storage-files-configure-p2s-vpn-linux.md)」を参照してください。
    - [サイト間 (S2S) VPN](../../vpn-gateway/design.md#s2smulti)。これは、Azure と組織のネットワークの間の VPN 接続です。 S2S VPN 接続を使用すると、Azure ファイル共有にアクセスする必要のあるクライアント デバイスごとにではなく、組織のネットワークでホストされている VPN サーバーまたはデバイスについて 1 回 VPN 接続を構成するだけで済みます。 S2S VPN 接続のデプロイを簡略化するには、[Azure Files で使用するサイト間 (S2S) VPN の構成](storage-files-configure-s2s-vpn.md)に関するページを参照してください。
- [ExpressRoute](../../expressroute/expressroute-introduction.md)。これにより、Azure とインターネットを経由しないオンプレミス ネットワークの間に定義されたルートを作成できます。 ExpressRoute はオンプレミスのデータセンターと Azure の間の専用のパスを提供するため、ExpressRoute は、ネットワーク パフォーマンスが考慮事項であるときに役立つことがあります。 ExpressRoute はまた、組織のポリシーまたは規制要件にクラウド内のリソースへの確定的なパスが必要な場合の適切なオプションでもあります。

Azure ファイル共有へのアクセスに使用するトンネリング方法に関係なく、ストレージ アカウントへのトラフィックを、通常のインターネット接続ではなく、確実にトンネルに通すためのメカニズムは必要です。 ストレージ アカウントのパブリック エンドポイントにルーティングすることは技術的には可能ですが、そのためには、特定のリージョン内にある Azure ストレージ クラスターの IP アドレスをすべてハードコーディングする必要があります。これは、ストレージ アカウントはストレージ クラスター間で随時移動される可能性があるためです。 また、新しいクラスターが常時追加されるので、IP アドレスのマッピングも絶えず更新する必要があります。

ストレージ アカウントの IP アドレスを VPN のルーティング規則にハードコーディングするのではなく、プライベート エンドポイントを使用することをお勧めします。そうすると、Azure 仮想ネットワークのアドレス空間の IP アドレスがストレージ アカウントに与えられます。 Azure へのトンネルを作成すると、オンプレミス ネットワークと 1 つ以上の仮想ネットワークの間にピアリングが確立されるので、適切なルーティングが永続的に可能になります。

### <a name="private-endpoints"></a>プライベート エンドポイント
Azure Files では、ストレージ アカウントの既定のパブリック エンドポイントに加え、1 つ以上のプライベート エンドポイントを使用することもできます。 プライベート エンドポイントは、Azure 仮想ネットワーク内でのみアクセス可能なエンドポイントです。 ストレージ アカウントのプライベート エンドポイントを作成すると、ストレージ アカウントには、仮想ネットワークのアドレス空間内のプライベート IP アドレスが与えられます。ちょうど、オンプレミスのファイル サーバーや NAS デバイスに、そのオンプレミス ネットワークの専用アドレス空間から IP アドレスが割り当てられるのと似ています。 

個々のプライベート エンドポイントは、Azure 仮想ネットワークの特定のサブネットに関連付けられます。 ストレージ アカウントには、複数の仮想ネットワーク内のプライベート エンドポイントを使用することができます。

Azure Files でプライベート エンドポイントを使用することにより、次のことが可能となります。
- オンプレミス ネットワークから、VPN または ExpressRoute 接続によるプライベートピアリングを使用して Azure ファイル共有に安全に接続します。
- パブリック エンドポイントでのすべての接続をブロックするようにストレージ アカウントのファイアウォールを構成することで、Azure ファイル共有をセキュリティで保護します。 既定では、プライベート エンドポイントを作成しても、パブリック エンドポイントへの接続はブロックされません。
- 仮想ネットワーク (およびピアリングの境界) からのデータの流出をブロックできるようにすることで、仮想ネットワークのセキュリティを強化します。

プライベート エンドポイントを作成する方法については、[Azure Files 用のプライベート エンドポイントの構成](storage-files-networking-endpoints.md)に関するページを参照してください。

### <a name="private-endpoints-and-dns"></a>プライベート エンドポイントと DNS
プライベート エンドポイントを作成すると、既定では、`privatelink` サブドメインに対応するプライベート DNS ゾーンも作成 (または既存のゾーンが更新) されます。 厳密には、ストレージ アカウントにプライベート エンドポイントを使用するためにプライベート DNS ゾーンの作成は必要ありませんが、一般にはこれを強くお勧めします。また、Active Directory のユーザー プリンシパルで Azure ファイル共有をマウントしたり、FileREST API からアクセスしたりする場合には、間違いなく必要です。

> [!Note]  
> この記事では、Azure パブリック リージョンを対象に、ストレージ アカウントの DNS サフィックス `core.windows.net` を使用しています。 この解説は、Azure ソブリン クラウド (Azure US Government クラウドや Azure China クラウドなど) にも当てはまります。ご利用の環境の適切なサフィックスに置き換えてください。 

プライベート DNS ゾーンに、`storageaccount.privatelink.file.core.windows.net` の A レコードを作成すると共に、`storageaccount.file.core.windows.net` というパターンに従って、ストレージ アカウントの通常の名前に対応する CNAME レコードを作成します。 Azure のプライベート DNS ゾーンは、プライベート エンドポイントを含んだ仮想ネットワークに接続されているため、Azure VM の PowerShell から `Resolve-DnsName` コマンドレット (Windows および Linux では `nslookup` でも可) を呼び出せば、DNS 構成を観察することができます。

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

この例では、ストレージ アカウント `storageaccount.file.core.windows.net` が、プライベート エンドポイントのプライベート IP アドレス (`192.168.0.4`) に解決されます。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

同じコマンドをオンプレミスから実行した場合、同じストレージ アカウント名が、ストレージ アカウントのパブリック IP アドレスに解決されることがわかります。`storageaccount.file.core.windows.net` は `storageaccount.privatelink.file.core.windows.net` の CNAME レコードで、さらにそれが、ストレージ アカウントをホストする Azure ストレージ クラスターの CNAME レコードになっています。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

このことは、ストレージ アカウントが、パブリック エンドポイントと 1 つ以上のプライベート エンドポイントの両方を公開できるという事実を表しています。 ストレージ アカウント名が確実にプライベート エンドポイントのプライベート IP アドレスに解決されるようにするには、オンプレミスの DNS サーバーの構成を変更する必要があります。 これはいくつかの方法で実行できます。

- `storageaccount.file.core.windows.net` が目的のプライベート エンドポイントのプライベート IP アドレスに解決されるように、クライアントの hosts ファイルを変更します。 これは、運用環境では決してお勧めできません。Azure ファイル共有をマウントする予定のすべてのクライアントに対してこれらの変更を行う必要が生じるからです。ストレージ アカウントまたはプライベート エンドポイントへの変更も自動的には処理されません。
- オンプレミスの DNS サーバーに `storageaccount.file.core.windows.net` の A レコードを作成します。 この方法の利点は、オンプレミス環境内のクライアントを個別に構成しなくても、クライアントが自動的にストレージ アカウントを解決できるようになることです。ただし、変更は反映されないので、hosts ファイルを編集するのと同様の弱点があります。 この解決策は完璧ではありませんが、環境によっては最適な選択肢となりえます。
- `core.windows.net` ゾーンをオンプレミスの DNS サーバーから Azure のプライベート DNS ゾーンに転送します。 Azure のプライベート DNS ホストには、特殊な IP アドレス (`168.63.129.16`) でアクセスできますが、このアドレスには、Azure のプライベート DNS ゾーンにリンクされた仮想ネットワーク内からしかアクセスできません。 この制限を回避するには、Azure のプライベート DNS ゾーンに `core.windows.net` を転送する別の DNS サーバーを仮想ネットワーク内で実行します。 この設定を簡略化するために、Azure 仮想ネットワークに DNS サーバーを自動デプロイしてそれらを必要に応じて構成する PowerShell コマンドレットを用意しています。 DNS 転送を設定する方法については、[Azure Files を使用した DNS の構成](storage-files-networking-dns.md)に関するページを参照してください。

## <a name="storage-account-firewall-settings"></a>ストレージ アカウントのファイアウォール設定
ファイアウォールは、ストレージ アカウントのパブリック エンドポイントへのアクセスをどの要求に許可するかを制御するネットワーク ポリシーです。 ストレージ アカウントのファイアウォールを使用すると、ストレージ アカウントのパブリック エンドポイントへのアクセスを特定の IP アドレスや IP アドレス範囲、または仮想ネットワークに制限することができます。 一般に、ストレージ アカウントのほとんどのファイアウォール ポリシーではネットワーク アクセスが 1 つ以上の仮想ネットワークに制限されます。 

ストレージ アカウントへのアクセスを仮想ネットワークに制限するには、次の 2 つの方法があります。
- ストレージ アカウントに 1 つ以上のプライベート エンドポイントを作成し、パブリック エンドポイントへのアクセスをすべて制限します。 これで、そのストレージ アカウント内の Azure ファイル共有にアクセスできるのは、特定の仮想ネットワーク内から送信されたトラフィックだけになります。
- パブリック エンドポイントを 1 つ以上の仮想ネットワークに制限します。 これは、"*サービス エンドポイント*" と呼ばれる仮想ネットワークの機能を使用して実現できます。 ストレージ アカウントへのトラフィックをサービス エンドポイントを使用して制限していても、ストレージ アカウントへのアクセスには、やはりパブリック IP アドレスを使用します。

> [!NOTE]
> NFS ファイル共有では、仮想ネットワーク経由でのみ、ストレージ アカウントのパブリック エンドポイントにアクセスできます。 NFS 共有では、ストレージ アカウントのパブリック エンドポイントに自由にアクセスできます。

ストレージ アカウントのファイアウォールを構成する方法の詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

## <a name="see-also"></a>関連項目
- [Azure Files の概要](storage-files-introduction.md)
- [Azure Files のデプロイの計画](storage-files-planning.md)
