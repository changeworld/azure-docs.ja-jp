---
title: Azure セキュリティ コントロール - データの復旧
description: セキュリティ コントロール データの復旧
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934507"
---
# <a name="security-control-data-recovery"></a>セキュリティ コントロールデータの復旧

定期的にすべてのシステム データ、構成、およびシークレットが自動的にバックアップされていることを確認します。

## <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 9.1 | 10.1 | Customer |

Azure Backup を有効にし、バックアップ ソース (Azure VM、SQL Server、またはファイル共有)、および必要な頻度と保持期間を構成します。

Azure Backup を有効にする方法:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 9.2 | 10.2 | Customer |

Azure Backup を有効にし、ターゲット VM および必要な頻度と保持期間を設定します。 Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

Azure Backup を有効にする方法:

https://docs.microsoft.com/azure/backup/

Azure で Key Vault のキーをバックアップする方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 9.3 | 10.3 | Customer |

Azure Backup 内でコンテンツのデータ復元を定期的に実行できるようにします。 必要に応じて、分離された VLAN への復元をテストします。 バックアップされたカスタマー マネージド キーの復元をテストします。

Azure 仮想マシンのバックアップからファイルを復旧する方法:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Azure で Key Vault のキーを復元する方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 9.4 | 10.4 | Customer |

オンプレミスのバックアップでは、Azure にバックアップする際に指定するパスフレーズを使用して保存時の暗号化が行われます。 Azure VM の場合、データは Storage Service Encryption (SSE) を使用して暗号化された上で保存されます。 Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護することができます。

Key Vault で論理的な削除を有効にする方法:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>次のステップ

次のセキュリティ コントロールを参照してください: [インシデント対応](security-control-incident-response.md)
