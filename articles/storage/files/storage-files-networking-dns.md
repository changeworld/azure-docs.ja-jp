---
title: Azure Files の DNS 転送の構成 | Microsoft Docs
description: Azure Files の DNS 転送を構成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8923b578f22f15dbf84b6e2ca33dfe14df38d4b
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687139"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Azure Files の DNS 転送の構成
Azure Files では、ファイル共有を含むストレージ アカウントのプライベート エンドポイントを作成することができます。 プライベート エンドポイントは、多くの異なるアプリケーションで役立ちますが、プライベート ピアリングを使用する VPN または ExpressRoute 接続を使って、オンプレミス ネットワークから Azure ファイル共有に接続する場合に特に便利です。 

ストレージ アカウントへの接続がネットワーク トンネルを経由するようにするには、ストレージ アカウントの完全修飾ドメイン名 (FQDN) がプライベート エンドポイントのプライベート IP アドレスに解決される必要があります。 これを実現するには、ストレージ エンドポイント サフィックス (パブリック クラウド リージョンの`core.windows.net`) を、仮想ネットワーク内からアクセスできる Azure プライベート DNS サービスに転送する必要があります。 このガイドでは、ストレージ アカウントのプライベート エンドポイント IP アドレスに適切に解決されるように、DNS 転送を設定して構成する方法を示します。

この記事で説明されている手順を行う前に、「[Azure Files のデプロイの計画](storage-files-planning.md)」および「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」をお読みいただくよう強くお勧めします。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png) |

## <a name="overview"></a>概要
Azure Files では、Azure ファイル共有にアクセスするための次の主な 2 種類のエンドポイントが提供されます。 
- パブリック エンドポイント。パブリック IP アドレスを持ち、世界中のどこからでもアクセスできます。
- プライベート エンドポイント。仮想ネットワーク内に存在し、その仮想ネットワークのアドレス空間内からのプライベート IP アドレスを持ちます。

パブリックおよびプライベート エンドポイントは、Azure ストレージ アカウントに存在します。 ストレージ アカウントは、複数のファイル共有だけでなく、BLOB コンテナーやキューなどのその他のストレージ リソースをデプロイできるストレージの共有プールを表す管理構造です。

すべてのストレージ アカウントに、完全修飾ドメイン名 (FQDN) があります。 パブリック クラウド リージョンの場合、この FQDN では `storageaccount.file.core.windows.net` パターンに従います。ここで、`storageaccount` はストレージ アカウントの名前です。 ワークステーションに共有をマウントするなど、この名前に対して要求を行うと、オペレーティング システムでは DNS 参照を実行し、完全修飾ドメイン名を、IP アドレスに解決させます。

既定では、`storageaccount.file.core.windows.net` はパブリック エンドポイントの IP アドレスに解決されます。 ストレージ アカウントのパブリック エンドポイントは、他の多くのストレージ アカウントのパブリック エンドポイントをホストする Azure ストレージ クラスターでホストされます。 プライベート エンドポイントを作成すると、プライベート DNS ゾーンは、追加された仮想ネットワークにリンクされます。その場合、ストレージ アカウントのプライベート エンドポイントのプライベート IP アドレス用の A レコード エントリに `storageaccount.file.core.windows.net` をマップする CNAME レコードを使用します。 これにより、仮想ネットワーク内で `storageaccount.file.core.windows.net` FQDN を使用して、プライベート エンドポイントの IP アドレスに解決させることができます。

ここでの究極の目標は、VPN や ExpressRoute 接続などのネットワーク トンネルを使用してオンプレミスからストレージ アカウント内でホストされている Azure ファイル共有にアクセスすることであるため、Azure Files サービスに対して行われた要求を Azure プライベート DNS サービスに転送するようにオンプレミス DNS サーバーを構成する必要があります。 これを実現するには、Azure 仮想ネットワーク内でホストされている DNS サーバーに対する、`*.core.windows.net` (または米国政府、ドイツ、あるいは中国の国内クラウド用の適切なストレージ エンドポイント サフィックス) の "*条件付き転送*" を設定する必要があります。 その後、この DNS サーバーでは、Azure のプライベート DNS サービスに要求を再帰的に転送します。これにより、ストレージ アカウントの完全修飾ドメイン名が適切なプライベート IP アドレスに解決されるようになります。

Azure Files の DNS 転送を構成するには、要求を転送するために DNS サーバーをホストする仮想マシンを実行する必要があります。しかし、これは、仮想ネットワーク内でホストされているすべての Azure ファイル共有に対する 1 回限りの手順です。 また、これは、Azure Files に対する排他的な要件ではありません。オンプレミスからアクセスするプライベート エンドポイントをサポートする次のすべての Azure サービスでは、このガイドで構成する DNS 転送を利用することができます: Azure Blob storage、SQL Azure、Cosmos DB など。 

このガイドでは、Azure ストレージ エンドポイントの DNS 転送を構成する手順を示します。したがって、Azure Files だけでなく、他のすべての Azure ストレージ サービス (Azure Blob storage、Azure Table storage、Azure Queue storage など) に対する DNS 名前解決要求が、Azure のプライベート DNS サービスに転送されます。 必要に応じて、他の Azure サービスのエンドポイントをさらに追加することもできます。 オンプレミス DNS サーバーへの DNS の再転送も構成されます。これにより、仮想ネットワーク内のクラウド リソース (DFS-R サーバーなど) でオンプレミスのマシン名を解決できるようになります。 

## <a name="prerequisites"></a>前提条件
Azure Files への DNS 転送を設定する前に、次の手順を完了しておく必要があります。

- マウントする Azure ファイル共有を含むストレージ アカウント。 ストレージ アカウントと Azure ファイル共有を作成する方法については、「[Azure ファイル共有を作成する](storage-how-to-create-file-share.md)」を参照してください。
- ストレージ アカウントのプライベート エンドポイント。 Azure Files のプライベート エンドポイントを作成する方法については、「[プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint)」を参照してください。
- [最新バージョン](/powershell/azure/install-az-ps)の Azure PowerShell モジュール。

> [!Important]  
> このガイドでは、オンプレミス環境の Windows Server 内で DNS サーバーを使用することを前提としています。 このガイドで説明されているすべての手順は、Windows DNS Server だけでなく、どの DNS サーバーでも使用できます。

## <a name="configuring-dns-forwarding"></a>DNS 転送の構成
Azure 仮想ネットワーク内に DNS サーバーが既に配置されている場合、または組織で使用される任意の方法で独自の仮想マシンを DNS サーバーに単にデプロイする場合、組み込みの DNS サーバーの PowerShell コマンドレットを使用して、DNS を構成できます。

オンプレミスの DNS サーバーで、`Add-DnsServerConditionalForwarderZone` を使用して条件付きフォワーダーを作成します。 この条件付きフォワーダーは、トラフィックを効果的に正しく Azure に転送するために、オンプレミスのすべての DNS サーバーにデプロイする必要があります。 `<azure-dns-server-ip>` は、必ず、実際の環境に適した IP アドレスに置き換えてください。

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Azure 仮想ネットワーク内の DNS サーバーでは、ストレージ アカウント DNS ゾーンに対する要求が Azure プライベート DNS サービスに送られるように、フォワーダーを配置する必要もあります。その場合、予約済み IP アドレスの `168.63.129.16` に置き換えられます (別の PowerShell セッション内でコマンドを実行する場合は、必ず、`$storageAccountEndpoint` を設定してください)。

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="confirm-dns-forwarders"></a>DNS フォワーダーを確認する
DNS フォワーダーが正しく適用されているかどうかをテストする前に、`Clear-DnsClientCache` を使用して、ローカル ワークステーションの DNS キャッシュをクリアすることをお勧めします。 ストレージ アカウントの完全修飾ドメイン名を正しく解決できるかどうかをテストするには、`Resolve-DnsName` または `nslookup` を使用します。

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud you're deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

名前解決に成功した場合、解決済みの IP アドレスがストレージ アカウントの IP アドレスと一致しているはずです。

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
```

SMB ファイル共有をマウントする場合、次の `Test-NetConnection` コマンドを使用して、ストレージ アカウントへの TCP 接続が正常に確立されたことを確認することもできます。

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>関連項目
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)
- [Azure Files ネットワーク エンドポイントの構成](storage-files-networking-endpoints.md)
