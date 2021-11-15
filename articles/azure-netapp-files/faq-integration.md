---
title: Azure NetApp Files の統合に関する FAQ | Microsoft Docs
description: 他の製品またはサービスで Azure NetApp Files を使用することに関する、よくある質問 (FAQ) に答えます。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: c48a35b4578488fdab5fc55cdca4e19d97a003d3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269910"
---
# <a name="integration-faqs-for-azure-netapp-files"></a>Azure NetApp Files の統合に関する FAQ

この記事では、他の製品またはサービスで Azure NetApp Files を使用することに関する、よくある質問 (FAQ) に答えます。  

## <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) で Azure NetApp Files NFS または SMB ボリュームを使用できますか?

Azure NetApp Files NFS ボリュームは、AVS Windows VM または Linux VM にマウントできます。 AVS Windows VM 上で Azure NetApp Files SMB 共有をマップできます。 詳しくは、「[Azure NetApp Files と Azure VMware Solution との統合]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md)」をご覧ください。  

## <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) で Azure NetApp Files NFS または SMB ボリュームを使用する場合、どのリージョンがサポートされますか?

AVS での Azure NetApp Files の NFS または SMB ボリュームを使用した *ゲスト OS のマウント* は、[AVS および ANF が有効なすべてのリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware,netapp)でサポートされています。

## <a name="does-azure-netapp-files-work-with-azure-policy"></a>Azure NetApp Files で Azure Policy を使用できますか。

はい。 Azure NetApp Files はファーストパーティーのサービスです。 Azure リソース プロバイダーの標準に完全に準拠しています。 そのため Azure NetApp Files は、*カスタム ポリシー定義* によって Azure Policy に統合できます。 Azure NetApp Files でカスタム ポリシーを使用する方法は、Microsoft Tech Community の [Azure NetApp Files での Azure Policy の使用](https://techcommunity.microsoft.com/t5/azure/azure-policy-now-available-for-azure-netapp-files/m-p/2282258)に関するページをご覧ください。 

## <a name="which-unicode-character-encoding-is-supported-by-azure-netapp-files-for-the-creation-and-display-of-file-and-directory-names"></a>ファイル名とディレクトリ名の作成と表示に、Azure NetApp Files でサポートされている Unicode 文字エンコードは何ですか?   

Azure NetApp Files で、NFS と SMB の両方のボリュームのファイル名とディレクトリ名についてサポートされているのは、UTF-8 Unicode 文字エンコード形式のみです。

UTF-8 によってサポートされていない非標準文字や絵文字などの補助文字やサロゲート ペアを名前に使用して、ファイルまたはディレクトリを作成しようとすると、操作は失敗します。 この場合、Windows クライアントからのエラーは、次のようなものになります: "指定したファイル名が有効ではないか長すぎます。 別のファイル名を指定してください。" 

## <a name="next-steps"></a>次のステップ  

- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
