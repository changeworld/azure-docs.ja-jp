---
title: Azure Managed Disks のサーバー側暗号化
description: Azure Storage では、保存時に暗号化してデータを保護してから、ストレージ クラスターに保存します。 カスタマー マネージド キーを使用し、独自のキーを使って暗号化を管理できます。また、マネージド ディスクの暗号化には Microsoft のマネージド キーを使用できます。
author: roygara
ms.date: 09/03/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 98bbc2f84b0f56c4e50e98e99e7bcaf86588fb48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803978"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure Disk Storage のサーバー側暗号化

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

ほとんどの Azure マネージド ディスクは Azure Storage 暗号化によって暗号化されています。暗号化では、サーバー側暗号化 (SSE) を使用してデータを保護し、組織のセキュリティとコンプライアンスのコミットメントを満たすことができます。 Azure Storage 暗号化では、Azure マネージド ディスク (OS およびデータ ディスク) に格納されているお使いのデータをクラウドに永続化するときに、既定で保存時に自動的に暗号化されます。 ただし、ホストでの暗号化が有効になっているディスクは、Azure Storage によって暗号化されません。 ホストでの暗号化が有効になっているディスクの場合、VM をホストしているサーバーがデータの暗号化を提供し、暗号化されたデータは Azure Storage に送信されます。

Azure マネージド ディスク内のデータは、利用できる最も強力なブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化され、FIPS 140-2 に準拠しています。 Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:次世代](/windows/desktop/seccng/cng-portal)」を参照してください。

Azure Storage の暗号化は、マネージド ディスクのパフォーマンスには影響しません。また、追加のコストはかかりません。 Azure Storage の暗号化の詳細については、「[Azure Storage の暗号化](../storage/common/storage-service-encryption.md)」をご参照ください。

> [!NOTE]
> 一時ディスクはマネージド ディスクではなく、ホストで暗号化を有効にしない限り、SSE によって暗号化されません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

プラットフォーム マネージド キーを利用してお使いのマネージド ディスクを暗号化することも、お使いの独自のキーを使用して暗号化を管理することもできます。 独自のキーを使用して暗号化を管理する場合は、マネージド ディスク内のすべてのデータの暗号化と暗号化解除に使用する *カスタマー マネージド キー* を指定できます。 

以降のセクションでは、キー管理の各オプションについてさらに詳しく説明します。

### <a name="platform-managed-keys"></a>プラットフォーム マネージド キー

既定では、マネージド ディスクはプラットフォーム マネージド暗号化キーを使用します。 すべてのマネージド ディスク、スナップショット、イメージ、および既存のマネージド ディスクに書き込まれるデータは、保存時に、プラットフォーム マネージド キーを使用して自動的に暗号化されます。

### <a name="customer-managed-keys"></a>カスタマー マネージド キー

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>制限

現在、カスタマー マネージド キーには次の制限があります。

- この機能がディスクで有効になっている場合、無効にすることはできません。
    これを回避する必要がある場合は、[Azure PowerShell モジュール](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)または [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) を使用して、 カスタマー マネージド キーを使用していないまったく別のマネージド ディスクにすべてのデータをコピーする必要があります。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>サポートされているリージョン

カスタマー マネージド キーは、マネージド ディスクが使用可能なすべてのリージョンで利用できます。

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

マネージド ディスクのカスタマー マネージド キーを有効にするには、[Azure PowerShell モジュール](windows/disks-enable-customer-managed-keys-powershell.md)、[Azure CLI](linux/disks-enable-customer-managed-keys-cli.md)、または [Azure portal](disks-enable-customer-managed-keys-portal.md) のいずれかを使用して有効にする方法について説明している記事を参照してください。 

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>ホストでの暗号化 - ご利用の VM データのエンドツーエンド暗号化

ホストで暗号化を有効にすると、VM ホスト自体 (ご利用の VM が割り当てられている Azure サーバー) でその暗号化が開始されます。 お使いの一時ディスクと OS およびデータ ディスクのキャッシュのデータは、その VM ホストに格納されます。 ホストでの暗号化を有効にした後、このデータはすべて保存時に暗号化され、暗号化された状態で永続化されているストレージ サービスに送られます。 基本的に、ホストでの暗号化では、データがエンドツーエンドで暗号化されます。 ホストでの暗号化では、お使いの VM の CPU が使用されないため、お使いの VM のパフォーマンスには影響しません。 

エンドツーエンド暗号化を有効にすると、一時ディスクとエフェメラル OS ディスクは保存時に、プラットフォーム マネージド キーを使用して暗号化されます。 OS とデータ ディスクのキャッシュは、選択されたディスクの暗号化の種類に応じて、カスタマー マネージド キーまたはプラットフォーム マネージド キーのいずれかを使用して保存時に暗号化されます。 たとえば、ディスクがカスタマー マネージド キーで暗号化されている場合、そのディスクのキャッシュはカスタマー マネージド キーで暗号化されます。ディスクがプラットフォーム マネージド キーで暗号化されている場合、そのディスクのキャッシュはプラットフォーム マネージド キーで暗号化されます。

### <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

また、プログラムによって VM のサイズを確認することもできます。 プログラムによってこれを取得する方法については、[Azure PowerShell モジュール](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes)または [Azure CLI](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes) の記事の「サポートされている VM のサイズを確認する」セクションを参照してください。

ホストで暗号化を使用してエンドツーエンドの暗号化を有効にするには、[Azure PowerShell モジュール](windows/disks-enable-host-based-encryption-powershell.md)、[Azure CLI](linux/disks-enable-host-based-encryption-cli.md)、または [Azure portal](disks-enable-host-based-encryption-portal.md) のいずれかを使用して暗号化を有効にする方法について説明している記事を参照してください。

## <a name="double-encryption-at-rest"></a>保存時の二重暗号化

特定の暗号化アルゴリズム、実装、または侵害されたキーに関連するリスクを懸念しているセキュリティを重視しているお客様が、プラットフォーム マネージド キーを使用した別の暗号化アルゴリズムおよびモードをインフラストラクチャ レイヤーで使用し、追加レイヤーでの暗号化を使用することを選択できるようになりました。 この新しいレイヤーは、二重暗号化を使用して保存時に暗号化される、永続化された OS およびデータ ディスク、スナップショット、イメージのすべてに適用できます。

### <a name="supported-regions"></a>サポートされているリージョン

マネージド ディスクが利用できるすべてのリージョンで二重暗号化が利用できます。

マネージド ディスクの保存時の二重暗号化を有効にするには、[Azure PowerShell モジュール](windows/disks-enable-double-encryption-at-rest-powershell.md)、[Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md)、または [Azure portal](disks-enable-double-encryption-at-rest-portal.md) のいずれかを使用して有効にする方法について説明している記事を参照してください。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>サーバー側の暗号化と Azure ディスク暗号化の比較

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) では、Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能または Windows の [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 機能を利用して、マネージド ディスクをゲスト VM 内のカスタマー マネージド キーを使用して暗号化します。  カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。
> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q: サーバー側の暗号化は、マネージド ディスクを作成するときに、既定で有効になりますか?**

**A:** はい。 マネージド ディスクは、サーバー側の暗号化とプラットフォーム マネージド キーを使用して暗号化されます。

**Q: ブート ボリュームは、マネージド ディスク上で既定で暗号化されていますか?**

**A:** はい。 既定では、OS ディスクを含むすべてのマネージド ディスクが暗号化されています。

**Q:だれが暗号化キーを管理するのですか?**

**A:** プラットフォーム マネージド キーは Microsoft によって管理されます。 Azure Key Vault に格納されている独自のキーを使用したり、管理したりすることもできます。

**Q: 自分のマネージド ディスクのサーバー側の暗号化を無効にすることはできますか?**

**A:** いいえ。

**Q: サーバー側の暗号化は、特定のリージョンのみで使用可能ですか?**

**A:** いいえ。 プラットフォーム マネージド キーとカスタマー マネージド キーの両方を使用するサーバー側の暗号化は、Azure Managed Disks が利用可能なすべてのリージョンで利用できます。

**Q: Azure Site Recovery では、オンプレミスから Azure および Azure から Azure へのディザスター リカバリー シナリオでカスタマー マネージド キーを使用するサーバー側の暗号化をサポートしていますか?**

**A:** はい。

**Q: Azure Backup サービスを使用して、カスタマー マネージド キーを使用するサーバー側の暗号化によって暗号化されたマネージド ディスクをバックアップできますか?**

**A:** はい。

**Q: マネージド スナップショットおよびイメージは、暗号化されますか?**

**A:** はい。 すべての管理スナップショットおよびイメージは、自動的に暗号化されます。

**Q: VM アンマネージド ディスクが、暗号化されているストレージ アカウントまたは以前に暗号化されていたストレージ アカウントに配置されている場合、それらのディスクをマネージド ディスクに変換できますか?**

**A:** はい。

**Q: マネージド ディスクまたはスナップショットからエクスポートされた VHD も暗号化されますか?**

**A:** いいえ。 ただし、暗号化されたマネージド ディスクまたはスナップショットから暗号化されたストレージ アカウントに VHD をエクスポートした場合は、暗号化されます。

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell モジュール](windows/disks-enable-host-based-encryption-powershell.md)、[Azure CLI](linux/disks-enable-host-based-encryption-cli.md)、または [Azure portal](disks-enable-host-based-encryption-portal.md) のいずれかで、ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にします。
- [Azure PowerShell モジュール](windows/disks-enable-double-encryption-at-rest-powershell.md)、[Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md)、または [Azure portal](disks-enable-double-encryption-at-rest-portal.md) のいずれかを使用して、マネージド ディスクの保存時の二重暗号化を有効にします。
- [Azure PowerShell モジュール](windows/disks-enable-customer-managed-keys-powershell.md)、[Azure CLI](linux/disks-enable-customer-managed-keys-cli.md)、または [Azure portal](disks-enable-customer-managed-keys-portal.md) のいずれかを使用して、マネージド ディスクのカスタマー マネージド キーを有効にします。
- [カスタマー マネージド キーで暗号化されたディスクを作成するための Azure Resource Manager テンプレートを探索する](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault とは](../key-vault/general/overview.md)
