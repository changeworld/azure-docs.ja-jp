---
title: Azure NetApp Files でのデータの移行と保護に関する FAQ | Microsoft Docs
description: Azure NetApp Files のデータの移行と保護についてよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c6422b99087b8239ea6d21c0f5186edd5df1c247
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269742"
---
# <a name="data-migration-and-protection-faqs-for-azure-netapp-files"></a>Azure NetApp Files でのデータの移行と保護に関するよくあるご質問

この記事では、Azure NetApp Files のデータの移行と保護についてよくあるご質問 (FAQ) に回答します。

## <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Azure NetApp Files にデータを移行するにはどうすればよいですか?
Azure NetApp Files には NFS ボリュームと SMB ボリュームがあります。  ファイル ベースのコピー ツールを使用すると、データをサービスに移行することができます。 

NetApp には、SaaS ベースのソリューションである [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service) があります。このソリューションでは、NFS または SMB のデータを、Azure NetApp Files の NFS エクスポートまたは SMB 共有にレプリケートすることができます。 

また、さまざまな無料のツールを使用してデータをコピーすることもできます。 NFS の場合は、[rsync](https://rsync.samba.org/examples.html) などのワークロード ツールを使用して、Azure NetApp Files ボリュームにソース データをコピーしたり同期したりできます。 SMB の場合は、ワークロード [robocopy](/windows-server/administration/windows-commands/robocopy) を同じ方法で使用することができます。  これらのツールでは、ファイルまたはフォルダーのアクセス許可をレプリケートすることもできます。 

オンプレミスから Azure NetApp Files へのデータ移行の要件は、次のとおりです。 

- Azure NetApp Files がターゲット Azure リージョンで利用できることを確認します。
- ソースと Azure NetApp Files のターゲット ボリュームの IP アドレス間のネットワーク接続を検証します。 オンプレミスと Azure NetApp Files サービスとの間のデータ転送は、ExpressRoute 経由でサポートされています。
- ターゲットの Azure NetApp Files ボリュームを作成します。
- 使用したいファイル コピー ツールを使用して、ターゲット ボリュームにソース データを転送します。

## <a name="where-does-azure-netapp-files-store-customer-data"></a>Azure NetApp Files では、顧客データはどこに格納されますか?   

既定では、Azure NetApp Files ボリュームをデプロイするリージョン内にデータがとどまります。 ただし、[リージョン間レプリケーション](cross-region-replication-introduction.md)を使って、使用できる宛先リージョンにデータをボリューム単位でレプリケートすることができます。

## <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Azure NetApp Files ボリュームのコピーを別の Azure リージョンに作成するには、どうすればよいですか?
    
Azure NetApp Files には NFS ボリュームと SMB ボリュームがあります。  ファイル ベースのコピー ツールは、Azure リージョン間でデータをレプリケートするために使用できます。 

[リージョン間レプリケーション](cross-region-replication-introduction.md)機能を使用すると、あるリージョン内の Azure NetApp Files ボリューム (ソース) から別のリージョン内の別の Azure NetApp Files ボリューム (宛先) にデータを非同期的にレプリケートできます。  さらに、[既存のボリュームのスナップショットを使用して新しいボリュームを作成](snapshots-restore-new-volume.md)できます。

NetApp には、SaaS ベースのソリューションである [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service) があります。このソリューションでは、NFS または SMB のデータを、Azure NetApp Files の NFS エクスポートまたは SMB 共有にレプリケートすることができます。 

また、さまざまな無料のツールを使用してデータをコピーすることもできます。 NFS の場合は、[rsync](https://rsync.samba.org/examples.html) などのワークロード ツールを使用して、Azure NetApp Files ボリュームにソース データをコピーしたり同期したりできます。 SMB の場合は、ワークロード [robocopy](/windows-server/administration/windows-commands/robocopy) を同じ方法で使用することができます。  これらのツールでは、ファイルまたはフォルダーのアクセス許可をレプリケートすることもできます。 

Azure NetApp Files ボリュームを別の Azure リージョンにレプリケートするための要件は次のとおりです。 
- Azure NetApp Files がターゲット Azure リージョンで利用できることを確認します。
- 各リージョン内の Vnet 間のネットワーク接続を検証します。 現在のところ、VNet 間のグローバル ピアリングはサポートされていません。  VNet 間の接続は、ExpressRoute 回線とリンクするか、S2S VPN 接続を使用することで確立することができます。 
- ターゲットの Azure NetApp Files ボリュームを作成します。
- 使用したいファイル コピー ツールを使用して、ターゲット ボリュームにソース データを転送します。

## <a name="is-migration-with-azure-data-box-supported"></a>Azure Data Box を使用した移行はサポートされていますか?

いいえ。 Azure Data Box では、現在のところ Azure NetApp Files はサポートされていません。 

## <a name="is-migration-with-azure-importexport-service-supported"></a>Azure Import/Export サービスを使用した移行はサポートされていますか?

いいえ。 Azure Import/Export サービスでは、現在のところ Azure NetApp Files はサポートされません。

## <a name="next-steps"></a>次のステップ  

- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)
