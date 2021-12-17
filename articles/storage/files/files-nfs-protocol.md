---
title: Azure Files での NFS ファイル共有
description: ネットワーク ファイル システム (NFS) プロトコルを使用して Azure Files でホストされているファイル共有について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 31aec4e32178b2f0ca7905c6d584df298dd2059a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524343"
---
# <a name="nfs-file-shares-in-azure-files"></a>Azure Files での NFS ファイル共有
Azure Files には、Azure ファイル共有のマウント用に、[サーバー メッセージ ブロック (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) プロトコルと[ネットワーク ファイル システム (NFS)](https://en.wikipedia.org/wiki/Network_File_System) プロトコルの 2 つの業界標準プロトコルが用意されています。 Azure Files を使用すると、ワークロードに最適なファイル システム プロトコルを選択できます。 Azure ファイル共有では、SMB と NFS の両方のプロトコルを使用した個々の Azure ファイル共有へのアクセスはサポートされませんが、同じストレージ アカウント内に SMB と NFS のファイル共有を作成することはできます。 すべてのファイル共有に対し、Azure Files により、ストレージのニーズに合わせたスケールアップが可能で、数千ものクライアントによって同時にアクセスできる、エンタープライズ レベルのファイル共有が提供されます。

この記事では、NFS Azure ファイル共有について説明します。 SMB の Azure ファイル共有の詳細については、「[Azure Files での SMB ファイル共有](files-smb-protocol.md)」を参照してください。

> [!IMPORTANT]
>  運用環境で NFS ファイル共有を使用する前に、[Azure NFS ファイル共有のトラブルシューティング](storage-troubleshooting-files-nfs.md)に関する記事で既知の問題の一覧を参照してください。

## <a name="common-scenarios"></a>一般的なシナリオ
NFS ファイル共有は、多くの場合、次のシナリオで使用されます。

- Linux または POSIX ファイル システム API (POSIX 準拠が不要な場合でも) を使用して記述された基幹業務アプリケーションなどの、Linux/UNIX ベースのアプリケーション用のバッキング ストレージ。
- POSIX 準拠のファイル共有、大文字小文字の区別、または Unix 形式のアクセス許可 (UID/GID) を必要とするワークロード。
- 新しいアプリケーションとサービスの開発 (特に、そのアプリケーションまたはサービスにランダム IO と階層ストレージの要件がある場合)。 

## <a name="features"></a>特徴
- 完全に POSIX に準拠したファイル システム。
- ハード リンクのサポート。
- シンボリック リンクのサポート。
- 現在、NFS ファイル共有では、[4.1 プロトコル仕様](https://tools.ietf.org/html/rfc5661)のほとんどの機能がサポートされています。 あらゆる種類の委任とコールバック、Kerberos 認証、転送中の暗号化など、一部の機能はサポートされていません。


## <a name="security-and-networking"></a>セキュリティとネットワーク
Azure Files に格納されるすべてのデータは、Azure Storage Service Encryption (SSE) を使用して保存時に暗号化されます。 Storage Service Encryption は Windows の BitLocker と同様に機能し、データはファイル システム レベルで暗号化されます。 データは Azure ファイル共有のファイル システムで暗号化されるため、ディスクにエンコードされた場合、Azure ファイル共有を読み書きするために、基になるクライアント上のキーにアクセスできる必要はありません。 保存時の暗号化は、SMB と NFS の両方のプロトコルに適用されます。

転送中の暗号化については、Azure では [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE) を使用して Azure データセンター間で転送中のすべてのデータに対して暗号化レイヤーが提供されます。 これにより、Azure データセンター間でデータが転送されるときには暗号化が存在します。 SMB プロトコルを使用する Azure Files とは異なり、NFS プロトコルを使用するファイル共有には、ユーザーベースの認証は用意されていません。 NFS 共有の認証は、構成されているネットワーク セキュリティ規則に基づいています。 このため、NFS 共有に対してセキュリティで保護された接続のみが確立されるように保証するには、サービス エンドポイントまたはプライベート エンドポイントのいずれかを使用する必要があります。 プライベート エンドポイントに加えて、オンプレミスからも共有にアクセスする必要がある場合は、VPN または ExpressRoute を設定する必要があります。 次のソースから発信されていない要求は拒否されます。

- [プライベート エンドポイント](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [ポイント対サイト (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [サイト間](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [制限付きパブリック エンドポイント](storage-files-networking-overview.md#storage-account-firewall-settings)

利用可能なネットワーク オプションの詳細については、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

## <a name="support-for-azure-storage-features"></a>Azure Storage 機能のサポート

次の表は、NFS 4.1 機能が有効になっているアカウントにおける Azure Storage 機能の現在のサポート レベルを示しています。 

サポートは継続的に拡張されるため、この表に示されている項目の状態は時間の経過と共に変化する可能性があります。

| ストレージ機能 | NFS 共有でサポート |
|-----------------|---------|
| [ファイル管理プレーン REST API](/rest/api/storagerp/file-shares) | ✔️ |
| [ファイル データ プレーン REST API](/rest/api/storageservices/file-service-rest-api)| ⛔ |
| 保存時の暗号化|   ✔️ |
| 転送中の暗号化| ⛔ |
| [LRS または ZRS の冗長性の種類](storage-files-planning.md#redundancy)|  ✔️ |
| [プライベート エンドポイント](storage-files-networking-overview.md#private-endpoints) | ✔️  |
| サブディレクトリのマウント|  ✔️ |
| [特定の Azure 仮想ネットワークへのネットワーク アクセスを許可](storage-files-networking-endpoints.md#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)|  ✔️  |
| [特定の IP アドレスへのネットワーク アクセスを許可](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#grant-access-from-an-internet-ip-range)| ⛔ |
| [Premium レベル](storage-files-planning.md#storage-tiers) |  ✔️  |
| [Standard レベル (ホット、クール、トランザクション最適化)](storage-files-planning.md#storage-tiers)| ⛔ |
| [POSIX アクセス許可](https://en.wikipedia.org/wiki/File-system_permissions#Notation_of_traditional_Unix_permissions)|  ✔️  |
| ルート スカッシュ|  ✔️  |
| [ID ベースの認証](storage-files-active-directory-overview.md) | ⛔ |
| [Azure ファイル共有の論理的な削除](storage-files-prevent-file-share-deletion.md) | ⛔  |
| [Azure File Sync](../file-sync/file-sync-introduction.md)| ⛔ |
| [Azure ファイル共有のバックアップ](../../backup/azure-file-share-backup-overview.md)| ⛔ |
| [Azure ファイル共有スナップショット](storage-snapshots-files.md)| ⛔ |
| [GRS または GZRS の冗長性の種類](storage-files-planning.md#redundancy)| ⛔ |
| [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)| ⛔ |
| Azure Storage Explorer| ⛔ |
| 既存のストレージ アカウントで NFS 共有を作成*| ⛔ |
| 16 個を超えるグループをサポート| ⛔ |

## <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="performance"></a>パフォーマンス
NFS Azure ファイル共有は、ソリッドステート ドライブ (SSD) にデータを格納する Premium ファイル共有でのみ提供されます。 NFS 共有の IOPS とスループットは、プロビジョニングされた容量に合わせてスケーリングされます。 IOPS、IO バースト、スループットの数式については、課金についての記事の「[プロビジョニング モデル](understanding-billing.md#provisioned-model)」セクションを参照してください。 IO の平均待機時間は、小さな IO サイズの 1 桁の短い方のミリ秒ですが、メタデータの平均待機時間は 1 桁の長い方のミリ秒です。 untar などのメタデータの負荷の高い操作や、WordPress などのワークロードでは、開く/閉じるの操作数が多いため、待機時間が長くなる可能性があります。

## <a name="workloads"></a>ワークロード
> [!IMPORTANT]
> 運用環境で NFS ファイル共有を使用する前に、[Azure NFS ファイル共有のトラブルシューティング](storage-troubleshooting-files-nfs.md)に関する記事で既知の問題の一覧を参照してください。

NFS は、SAP アプリケーション レイヤー、データベース バックアップ、データベース レプリケーション、メッセージング キュー、汎用ファイル サーバーのホーム ディレクトリ、アプリケーション ワークロードのコンテンツ リポジトリなどのワークロードで正常に動作することが検証されています。

次のワークロードには既知の問題があります。 既知の問題の一覧については、「[Azure NFS ファイル共有に関するトラブルシューティング](storage-troubleshooting-files-nfs.md)」という記事を参照してください。
- Oracle Database で、dNFS 機能との非互換性が生じる。


## <a name="next-steps"></a>次の手順
- [NFS ファイル共有を作成する](storage-files-how-to-create-nfs-shares.md)
- [NFS を使用した Azure Files、Blob Storage、および Azure NetApp Files へのアクセスを比較する](../common/nfs-comparison.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
