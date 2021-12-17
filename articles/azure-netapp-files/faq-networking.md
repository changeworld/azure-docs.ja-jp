---
title: Azure NetApp Files のネットワークに関する FAQ | Microsoft Docs
description: Azure NetApp Files のネットワークについてよく寄せられる質問 (FAQ) とその回答を紹介します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 11/08/2021
ms.openlocfilehash: 9539cf9cc327cb954a852c183cf8c94ee4d56ca1
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054792"
---
# <a name="networking-faqs-for-azure-netapp-files"></a>Azure NetApp Files のネットワークに関する FAQ

この記事では、Azure NetApp Files のネットワークについてよく寄せられる質問 (FAQ) とその回答を紹介します。 

## <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>NFS または SMB 用のデータ パスはインターネットを経由しますか?  

いいえ。 NFS または SMB 用のデータ パスはインターネットを経由しません。 Azure NetApp Files は、サービスが使用可能な Azure Virtual Network (VNet) にデプロイされている Azure のネイティブ サービスです。 Azure NetApp Files は、委任されたサブネットを使用し、VNet 上に直接ネットワーク インターフェイスをプロビジョニングします。 

詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。  

## <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>既に作成済みの VNet を Azure NetApp Files サービスに接続できますか?

はい。作成済みの VNet をサービスに接続することができます。 

詳細については、「[Azure NetApp Files のネットワーク計画のガイドライン](./azure-netapp-files-network-topologies.md)」を参照してください。  

## <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>DNS FQDN 名を使用して Azure NetApp Files の NFS ボリュームをマウントできますか?

はい。必要な DNS エントリを作成する場合は、できます。 Azure NetApp Files には、プロビジョニングされたボリューム用の サービス IP が用意されています。 

> [!NOTE] 
> Azure NetApp Files は、必要に応じてサービスの追加の IP をデプロイできます。  DNS エントリの定期的な更新が必要になる場合があります。

## <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームの独自の IP アドレスを設定または選択できますか?  

いいえ。 Azure NetApp Files ボリュームへの IP 割り当ては動的です。 静的 IP の割り当てはサポートされていません。 

## <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Azure NetApp Files はデュアル スタック (IPv4 および IPv6) VNet をサポートしていますか?

いいえ。Azure NetApp Files は現時点ではデュアル スタック (IPv4 および IPv6) の委任されたサブネットへのボリュームのデプロイをサポートしていません。 Azure NetApp Files サービスの委任されたサブネットは、IPv4 専用サブネットである必要があります。 ただし、デュアルスタック サブネットまたは (ピアリングされた) VNet を介して IPv4 経由で Azure NetApp Files にアクセスできます。

## <a name="is-the-number-of-the-ip-addresses-using-azure-vmware-solutions-for-guest-os-mounts-limited-to-1000"></a>ゲスト OS マウントのために Azure VMware Solution を使用する IP アドレスの数は 1,000 に制限されていますか?

いいえ。 Azure VMware Solution は ER ゲートウェイの内側にあるため、オンプレミスのシステムと同様の動作をします。 AVS の "ホスト" と "ゲスト" の数は Azure NetApp Files には認識されないため、[IP アドレスの 1,000 件の制限](azure-netapp-files-resource-limits.md#resource-limits)は適用されません。
 
## <a name="next-steps"></a>次のステップ  

- [Microsoft Azure ExpressRoute に関する FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network に関する FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)
