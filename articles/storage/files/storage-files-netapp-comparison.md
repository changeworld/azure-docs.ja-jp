---
title: Azure Files と Azure NetApp Files の比較 | Microsoft Docs
description: Azure Files と Azure NetApp Files の比較。
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: jeffpatt
ms.openlocfilehash: 06c1c1e32c8e31f1762f3fbdc932612036c5a327
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519289"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure Files と Azure NetApp Files の比較

この記事では、Azure Files と Azure NetApp Files を比較して説明します。 

クラウド ファイル ストレージを必要とするほとんどのワークロードは、Azure Files と Azure NetApp Files のどちらを使用しても適切に機能します。 お使いのワークロードにどちらが最適であるかを判断するには、この記事の情報を参照してください。 詳しくは [Azure Files](./index.yml) と [Azure NetApp Files](../../azure-netapp-files/index.yml) のドキュメント、および、[企業のすべてのファイル ワークロードのための共有ストレージ](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s) セッションをご覧ください。このセッションでは、Azure Files と Azure NetApp Files のどちらを選ぶべきか判断する方法を説明しています。

## <a name="features"></a>特徴

| カテゴリ | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| 説明 | [Azure Files](https://azure.microsoft.com/services/storage/files/) は、フル マネージド、高可用性、エンタープライズグレードのサービスであり、インプレース データ更新を使用するランダム アクセス ワークロード用に最適化されています。<br><br> Azure Files は、Azure BLOB などの他のサービスと同じ Azure Storage プラットフォーム上に構築されています。 | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) はフル マネージドの高可用性かつエンタープライズ グレードの NAS サービスであり、高度なデータ管理機能を必要とする、要求の厳しい、高パフォーマンスかつ低遅延のワークロードを処理できます。 これを使用すれば、他の手段では移行できないワークロードを移行できます。<br><br>  ANF は NetApp のベアメタルを利用して開発され、ONTAP ストレージ OS を Azure データセンター内で稼働することで、安定した Azure サービスとオンプレミスに近いパフォーマンスを実現します。 |
| プロトコル | Premium<br><ul><li>SMB 2.1、3.0、3.1.1</li><li>NFS 4.1</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1、3.0、3.1.1</li><li>REST</li></ul><br> 詳細については、「[使用可能なファイル共有プロトコル](./storage-files-planning.md#available-protocols)」を参照してください。 | すべてのレベル<br><ul><li>SMB 2.x、3.x</li><li>NFS 3.0、4.1</li><li>デュアル プロトコル アクセス (NFSv3/SMB)</li></ul><br> 詳細については、[NFS](../../azure-netapp-files/azure-netapp-files-create-volumes.md)、[SMB](../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)、または[デュアル プロトコル](../../azure-netapp-files/create-volumes-dual-protocol.md) ボリュームの作成方法を参照してください。 |
| 利用可能なリージョン | Premium<br><ul><li>30 リージョン以上</li></ul><br>Standard<br><ul><li>すべてのリージョン</li></ul><br> 詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)」を参照してください。 | すべてのレベル<br><ul><li>28 リージョン以上</li></ul><br> 詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)」を参照してください。 |
| 冗長性 | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> 詳細については、「[冗長性](./storage-files-planning.md#redundancy)」を参照してください。 | すべてのレベル<br><ul><li>組み込みのローカル HA</li><li>[リージョン間レプリケーション](../../azure-netapp-files/cross-region-replication-introduction.md)</li></ul> |
| サービス レベル アグリーメント (SLA)<br><br> Azure Files と Azure NetApp Files の SLA は異なる方法で計算されることに注意してください。 | [Azure Files の SLA](https://azure.microsoft.com/support/legal/sla/storage/) | [Azure NetApp Files の SLA](https://azure.microsoft.com/support/legal/sla/netapp) |  
| ID ベースの認証と認可 | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> ID ベースの認証は、SMB プロトコルを使用している場合にのみサポートされることに注意してください。 詳細については、「[よくあるご質問](./storage-files-faq.md#security-authentication-and-access-control)」をご覧ください。 | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> NFS/SMB デュアル プロトコル<ul><li>ADDS/LDAP 統合</li></ul><br>NFSv3/NFSv4.1<ul><li>NFS 拡張グループとの ADDS/LDAP 統合 [(プレビュー)](../../azure-netapp-files/configure-ldap-extended-groups.md)</li></ul><br> 詳細については、[Azure NetApp Files の NFS FAQ](../../azure-netapp-files/faq-nfs.md) に関する記事と、[Azure NetApp Files の SMB FAQ](../../azure-netapp-files/faq-smb.md) に関する記事を参照してください。 |
| 暗号化 | すべてのプロトコル<br><ul><li>カスタマーまたは Microsoft マネージド キーを使用した保存時の暗号化 (AES 256)</li></ul><br>SMB<br><ul><li>AES 256 または RC4-HMAC を使用した Kerberos 暗号化</li><li>転送中の暗号化</li></ul><br>REST<br><ul><li>転送中の暗号化</li></ul><br> 詳細については、[セキュリティとネットワーク](files-nfs-protocol.md#security-and-networking)に関するページを参照してください。 | すべてのプロトコル<br><ul><li>Microsoft マネージド キーを使用した保存時の暗号化 (AES 256) </li></ul><br>SMB<ul><li>AES-CCM (SMB 3.0) と AES-GCM (SMB 3.1.1) を使用した転送中の暗号化</li></ul><br>NFS 4.1<ul><li>AES 256 による Kerberos を使用した転送中の暗号化</li></ul><br> 詳細については、「[セキュリティに関する FAQ](../../azure-netapp-files/faq-security.md)」を参照してください。 |
| アクセス オプション | <ul><li>インターネット</li><li>セキュリティで保護された VNet アクセス</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> 詳細については、[ネットワークの考慮事項](./storage-files-networking-overview.md)に関する記事を参照してください。 | <ul><li>セキュリティで保護された VNet アクセス</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[グローバル ファイル キャッシュ](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](../../hpc-cache/hpc-cache-overview.md)</li></ul><br> 詳細については、[ネットワークの考慮事項](../../azure-netapp-files/azure-netapp-files-network-topologies.md)に関する記事を参照してください。 |
| データ保護  | <ul><li>増分スナップショット</li><li>ファイルまたはディレクトリ ユーザーの自己復元</li><li>新しい場所に復元</li><li>インプレース復元</li><li>共有レベルの論理的な削除</li><li>Azure Backup 統合</li></ul><br> 詳細については、「[Azure Files でのデータ保護機能の拡張](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/)」を参照してください。 | <ul><li>スナップショット (255/ボリューム)</li><li>ファイルまたはディレクトリ ユーザーの自己復元</li><li>新しいボリュームに復元</li><li>インプレース復元</li><li>[リージョン間レプリケーション](../../azure-netapp-files/cross-region-replication-introduction.md) </li></ul><br> 詳細については、「[Azure NetApp Files スナップショットのしくみ](../../azure-netapp-files/snapshots-introduction.md)」を参照してください。 |
| 移行ツール  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>記憶域移行サービス</li><li>AzCopy</li><li>Robocopy</li></ul><br> 詳細については、「[Azure ファイル共有への移行](./storage-files-migration-overview.md)」を参照してください。 | <ul><li>[グローバル ファイル キャッシュ](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service)、[XCP](https://xcp.netapp.com/)</li><li>記憶域移行サービス</li><li>AzCopy</li><li>Robocopy</li><li>アプリケーション ベース (HSR、Data Guard、AOAG など)</li></ul> |
| 層 | <ul><li>Premium</li><li>トランザクション最適化</li><li>ホット</li><li>クール</li></ul><br> 詳細については、「[ストレージ層](./storage-files-planning.md#storage-tiers)」を参照してください。 | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> すべてのレベルで最小待機時間は 1 ミリ秒未満となっています。<br><br> 詳細については、[サービス レベル](../../azure-netapp-files/azure-netapp-files-service-levels.md)と[パフォーマンスの考慮事項](../../azure-netapp-files/azure-netapp-files-performance-considerations.md)に関する記事を参照してください。 |
| 価格 | [Azure Files の料金](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>スケーラビリティとパフォーマンス

| カテゴリ | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| 共有またはボリュームの最小サイズ | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>最小なし。</li></ul> | すべてのレベル<br><ul><li>100 GiB (容量プールの最小サイズ: 4 TiB)</li></ul> |
| 共有またはボリュームの最大サイズ | 100 TiB | すべてのレベル<br><ul><li>100 TiB (容量プール上限: 500 TiB)</li></ul><br>Azure NetApp アカウントごとに最大 12.5 PiB まで |
| 共有またはボリュームの最大 IOPS | Premium<br><ul><li>最大 100 k</li></ul><br>Standard<br><ul><li>最大 20K</li></ul> | Ultra および Premium<br><ul><li>最大 450 k </li></ul><br>Standard<br><ul><li>最大 320 k</li></ul> |
| 共有またはボリュームの最大スループット | Premium<br><ul><li>最大 10 GiB/秒</li></ul><br>Standard<br><ul><li>最大 300 MiB/秒</li></ul> | Ultra および Premium<br><ul><li>最大 4.5 GiB/秒</li></ul><br>Standard<br><ul><li>最大 3.2 GiB/秒</li></ul> |
| 最大ファイル サイズ | 4 TiB | 16 TiB |
| ファイルあたりの最大 IOPS | Premium<br><ul><li>最大 8,000</li></ul><br>Standard<br><ul><li>1,000</li></ul> | すべてのレベル<br><ul><li>ボリューム上限まで</li></ul> |
| ファイルあたりの最大スループット | Premium<br><ul><li>300 MiB/秒 (SMB マルチチャネルで最大 1 GiB/秒)</li></ul><br>Standard<br><ul><li>60 MiB/秒</li></ul> | すべてのレベル<br><ul><li>ボリューム上限まで</li></ul> |
| SMB マルチチャネル | はい | はい |
| Latency | 1 ミリ秒の最小待機時間 (小さい IO の場合は 2 ミリ秒 から 3 ミリ秒) | 1 ミリ秒未満の最小待機時間 (ランダム IO の場合は 1 ミリ秒未満)<br><br>詳細については、[パフォーマンス ベンチマーク](../../azure-netapp-files/performance-benchmarks-linux.md)に関する記事を参照してください。 |

スケーラビリティとパフォーマンスの目標について詳しくは、[Azure Files](./storage-files-scale-targets.md#azure-files-scale-targets) と [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-resource-limits.md) に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
* [Azure Files のドキュメント](./index.yml)
* [Azure NetApp Files のドキュメント](../../azure-netapp-files/index.yml)
* [すべてのエンタープライズ ファイルワークロードの共有ストレージ セッション](https://www.youtube.com/watch?v=MJEbmITLwwU&t=4s)
