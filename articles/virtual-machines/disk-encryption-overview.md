---
title: マネージド ディスク暗号化オプションの概要
description: マネージド ディスク暗号化オプションの概要
author: msmbaldwin
ms.date: 06/05/2021
ms.topic: conceptual
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 989a7fcc058284c1c706cbfc30c97a77200de111
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315314"
---
# <a name="overview-of-managed-disk-encryption-options"></a>マネージド ディスク暗号化オプションの概要

マネージド ディスクに使用できる暗号化には、Azure Disk Encryption (ADE)、Server-Side Encryption (SSE)、ホストでの暗号化など、いくつかの種類があります。

- **Azure Disk Encryption** は、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 ADE では、Linux の [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) 機能または Windows の [BitLocker](https://wikipedia.org/wiki/BitLocker) 機能を使用して、Azure 仮想マシン (VM) の OS およびデータ ディスクのボリュームを暗号化します。 ADE は、ディスクの暗号化キーとシークレットを制御および管理できるように、Azure Key Vault と統合されています。  詳細については、「[Linux VM に対する Azure Disk Encryption](./linux/disk-encryption-overview.md)」または「[Windows VM 用の Azure Disk Encryption](./windows/disk-encryption-overview.md)」を参照してください。

- **Server-Side Encryption** (保存時の暗号化または Azure Storage 暗号化とも呼ばれます) により、Azure マネージド ディスク (OS およびデータ ディスク) 上の格納データはクラウドに保持されるときに自動的に暗号化されます。  詳細については、「[Azure Disk Storage のサーバー側暗号化](./disk-encryption.md)」を参照してください。

- **ホストでの暗号化** により、VM ホスト上の格納データは保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化が有効になっているディスクは、SSE では暗号化されません。代わりに、VM をホストしているサーバーにより、データが暗号化され、その暗号化されたデータが Azure Storage に送られます。詳細については、「[ホストでの暗号化 - ご利用の VM データのエンドツーエンド暗号化](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)」を参照してください。

## <a name="comparison"></a>比較

SSE、ADE、ホストでの暗号化の比較を次に示します。

| | 保存時の暗号化 (OS およびデータ ディスク) | 一時ディスクの暗号化 | キャッシュの暗号化 | コンピューティングとストレージ間で暗号化されたデータ フロー | お客様によるキーの制御 | Microsoft Defender for Cloud でのディスク暗号化の状態 |
|--|--|--|--|--|--|--|
| **プラットフォーム マネージド キーを使用した保存時の暗号化 (SSE+PMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#10060; | 異常 (除外時は適用不可) |
| **カスタマー マネージド キーを使用した保存時の暗号化 (SSE+CMK)** | &#x2705; | &#10060; | &#10060; | &#10060; | &#x2705; | 異常 (除外時は適用不可) |
| **Azure Disk Encryption** | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | Healthy |
| **ホストでの暗号化**  | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | 異常 (除外時は適用不可) |

> [!Important]
> ホストでの暗号化では、Microsoft Defender for Cloud は暗号化の状態を検出しません。

## <a name="next-steps"></a>次のステップ

- [Linux VM に対する Azure Disk Encryption](./linux/disk-encryption-overview.md)
- [Windows VM 用の Azure Disk Encryption](./windows/disk-encryption-overview.md)
- [Azure Disk Storage のサーバー側暗号化](./disk-encryption.md)
- [ホストでの暗号化](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure セキュリティの基礎 - Azure の暗号化の概要](../security/fundamentals/encryption-overview.md)
