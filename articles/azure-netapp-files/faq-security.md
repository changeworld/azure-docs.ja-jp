---
title: Azure NetApp Files のセキュリティに関する FAQ | Microsoft Docs
description: Azure NetApp Files のセキュリティについてのよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.custom: references_regions
ms.openlocfilehash: d1794516cfd6c97411d27122749a4260bcbe8358
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270019"
---
# <a name="security-faqs-for-azure-netapp-files"></a>Azure NetApp Files のセキュリティに関するよくあるご質問

この記事では、Azure NetApp Files のセキュリティについてのよくあるご質問 (FAQ) に回答します。

## <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Azure VM とストレージ間のネットワーク トラフィックは暗号化できますか?

Azure NetApp Files のデータ トラフィックは、パブリック エンドポイントが提供されず、データ トラフィックは顧客所有の VNet 内に留まっているので、設計上、本質的にセキュリティが確保されています。 既定では、転送中のデータは暗号化されません。 ただし、(NFS または SMB クライアントを実行している) Azure VM から Azure NetApp Files へのデータ トラフィックは、Azure VM 間のトラフィックと同様に安全です。 

NFSv3 プロトコルでは暗号化がサポートされていないため、この転送中のデータを暗号化することはできません。 ただし、NFSv4.1 と SMB3 による転送中のデータの暗号化は、必要に応じて有効にすることができます。 NFSv4.1 クライアントと Azure NetApp Files ボリューム間のデータ トラフィックは、AES-256 暗号化を使用した Kerberos を使用して暗号化できます。 詳細については、「[Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)」を参照してください。 SMB3 クライアントと Azure NetApp Files ボリュームの間のデータ トラフィックは、SMB 3.0 では AES-CCM アルゴリズムを使用して、SMB 3.1.1 接続では AES-GCM アルゴリズムを使用して、暗号化できます。 詳細については、「[Azure NetApp Files の SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)」を参照してください。 

## <a name="can-the-storage-be-encrypted-at-rest"></a>ストレージは保存時に暗号化できますか?

Azure NetApp Files のすべてのボリュームは、FIPS 140-2 標準を使用して暗号化されます。 すべてのキーは Azure NetApp Files サービスによって管理されています。 

## <a name="is-azure-netapp-files-cross-region-replication-traffic-encrypted"></a>Azure NetApp Files のリージョン間レプリケーションのトラフィックは暗号化されますか?

Azure NetApp Files のリージョン間レプリケーションでは、TLS 1.2 AES-256 GCM 暗号化を使用して、ソース ボリュームと宛先ボリュームの間で転送されるすべてのデータを暗号化します。 この暗号化は、Azure NetApp Files のリージョン間レプリケーションを含むすべての Azure トラフィックに対して既定で有効になっている [Azure MACSec 暗号化](../security/fundamentals/encryption-overview.md)に加えて行われます。 

## <a name="how-are-encryption-keys-managed"></a>暗号化キーはどのように管理されるのですか? 

Azure NetApp Files のキー管理は、サービスによって処理されます。 ボリュームごとに一意の XTS-AES-256 データ暗号化キーが生成されます。 すべてのボリューム キーは、暗号化キーの階層を使用して暗号化され、保護されます。 これらの暗号化キーは決して表示されず、また、暗号化されていない形式でレポートされることもありません。 ボリュームが削除されると暗号化キーは直ちに削除されます。

Azure Dedicated HSM を使用したカスタマー マネージド キー (Bring Your Own Key) のサポートは、制御ベースで、米国東部、米国中南部、米国西部 2、および US Gov バージニアの各リージョンで利用できます。 [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) にアクセスを要求することもできます。 容量が利用可能になると、要求が承認されます。

## <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>NFS エクスポート ポリシー規則を、Azure NetApp Files サービスのマウント ターゲットへのアクセスを制御するように構成できますか?

はい。1 つの NFS エクスポート ポリシーで、最大 5 つの規則を構成できます。

## <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Azure NetApp Files で Azure RBAC を使用できますか?

はい。Azure NetApp Files では、Azure RBAC の機能がサポートされています。 組み込みの Azure ロールに加え、Azure NetApp Files 用に[カスタム ロールを作成](../role-based-access-control/custom-roles.md)できます。 

Azure NetApp Files のアクセス許可の詳細な一覧については、「Azure リソース プロバイダーの操作」で「[`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp)」を参照してください。

## <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Azure NetApp Files で Azure アクティビティ ログはサポートされていますか?

Azure NetApp Files は Azure のネイティブ サービスです。 Azure NetApp Files に対する PUT、POST、および DELETE API がすべてログに記録されます。 たとえば、スナップショットを作成したユーザー、ボリュームを変更したユーザーといったアクティビティがログに表示されます。

API 操作の詳細な一覧については、「[Azure NetApp Files REST API](/rest/api/netapp/)」を参照してください。

## <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Azure NetApp Files で Azure のポリシーを使用できますか?

はい、[カスタム Azure ポリシー](../governance/policy/tutorials/create-custom-policy-definition.md)を作成することができます。 

ただし、Azure NetApp Files インターフェイスに Azure ポリシー (カスタム名前付けポリシー) を作成することはできません。 「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md#considerations)」を参照してください。

## <a name="when-i-delete-an-azure-netapp-files-volume-is-the-data-deleted-safely"></a>Azure NetApp Files のボリュームを削除するとき、データは安全に削除されますか? 

Azure NetApp Files ボリュームの削除は、プログラムによってただちに実行されます。 削除操作には、保存データの暗号化に使用されるキーの削除が含まれます。 削除操作が正常に実行された後では、(Azure portal や API などのインターフェイスを使用して) 削除されたボリュームを回復するためのシナリオはありません。


## <a name="next-steps"></a>次のステップ  

- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)