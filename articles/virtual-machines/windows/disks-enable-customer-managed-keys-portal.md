---
title: Azure portal を使用し、SSE でカスタマー マネージド キーを有効にする - マネージド ディスク
description: Azure portal を使用して、マネージド ディスクでカスタマー マネージド キーを使用し、サーバー側の暗号化を有効にします。
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4e3699e1fab928db5a466073c411b701ffc1a51d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235702"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>サーバー側の暗号化でカスタマー マネージド キーを有効にする - マネージド ディスク - ポータル

Azure Disk Storage を使用すると、選択した場合は、マネージド ディスクにサーバー側の暗号化 (SSE) を使用しているときに独自のキーを管理できます。 カスタマー マネージド キーを使用する SSE とその他のマネージド ディスクの暗号化の概念については、ディスクの暗号化の記事の「[カスタマー マネージド キー](disk-encryption.md#customer-managed-keys)」セクションを参照してください。

## <a name="restrictions"></a>制限

現在、カスタマー マネージド キーには次の制限があります。

- この機能がディスクで有効になっている場合、無効にすることはできません。
    これを回避する必要がある場合は、カスタマー マネージド キーを使用していないまったく別のマネージド ディスクに[すべてのデータをコピー](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)する必要があります。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

以下のセクションには、マネージド ディスクに対してカスタマー マネージド キーを有効にして使用する方法が含まれています。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [カスタマー マネージド キーで暗号化されたディスクを作成するための Azure Resource Manager テンプレートを探索する](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault とは](../../key-vault/general/overview.md)
- [カスタマー マネージド キー対応ディスクを含むコンピューターをレプリケートする](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell を使用して Azure への VMware VM のディザスター リカバリーを設定する](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell と Azure Resource Manager を使用して Azure への Hyper-V VM のディザスター リカバリーを設定する](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)