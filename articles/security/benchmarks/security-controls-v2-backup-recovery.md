---
title: Azure セキュリティ ベンチマーク V2 - バックアップと回復
description: Azure セキュリティ ベンチマーク V2 バックアップと回復
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b2e54545fb79120a3f9d66067da267df3b151b3f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322123"
---
# <a name="security-control-v2-backup-and-recovery"></a>セキュリティ コントロール V2:バックアップと回復

バックアップと回復により、データと構成のバックアップがさまざまなサービス レベルで確実に実行、検証、保護されるようにコントロールがカバーされています。

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1:定期的な自動バックアップを保証する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| BR-1 | 10.1 | CP-2、CP4、CP-6、CP-9 |

予期しないイベントが発生した後もビジネスが継続性されるよう、システムとデータが確実にバックアップします。 これは、目標復旧時点 (RPO) と目標復旧時間 (RTO) のすべての目標から定義する必要があります。

Azure Backup を有効にし、(Azure VM、SQL Server、HANA データベースまたはファイル共有などの) バックアップ ソース、希望の頻度および保持期間を構成します。  

geo 冗長ストレージ オプションを有効にして、セカンダリ リージョンにバックアップ データをレプリケートし、リージョン間での復元を使用して復旧されるように、保護レベルを高くすることもできます。

- [エンタープライズ規模の事業継続とディザスター リカバリー](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Azure Backup を有効にする方法](/azure/backup/)

- [リージョンをまたがる復元を有効にする方法](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ポリシーと標準](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2:バックアップ データを暗号化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

攻撃に対し、自分のバックアップが確実に保護されるようにする必要があります。 これには、機密性が失われるのを防ぐためにバックアップを暗号化することも含まれます。   

Azure Backup を使用するオンプレミスのバックアップによって、指定したパスフレーズを使用した保存時の暗号化が提供されます。 通常の Azure サービスのバックアップでは、バックアップ データは Azure プラットフォーム マネージド キーを使用して自動的に暗号化されます。 バックアップに、カスタマー マネージド キーを選択することもできます。 この場合は、キー コンテナー内のこのカスタマー マネージド キーも確実にバックアップ対象にします。 

バックアップおよびカスタマー マネージド キーを保護するには、Azure Backup、Azure Key Vault、またはその他のリソースでロールベースのアクセス制御を使用します。 さらに、バックアップが変更または削除される前に MFA を求める、高度なセキュリティ機能を有効にすることもできます。

- [Azure Backup のセキュリティ機能の概要](/azure/backup/security-overview)

- [カスタマー マネージド キーを使用したバックアップ データの暗号化](/azure/backup/encryption-at-rest-with-cmk) 

- [Azure 上でキー コンテナーのキーをバックアップする方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [ハイブリッド バックアップを攻撃から保護するためのセキュリティ機能](/azure/backup/backup-azure-security-feature#prevent-attacks)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3:カスタマー マネージド キーを含むすべてのバックアップを検証する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| BR-3 | 10.3 | CP-4、CP-9 |

自分のバックアップ データを定期的に復旧します。 バックアップされたカスタマー マネージド キーを復元できることを確認します。

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](/azure/backup/backup-azure-restore-files-from-vm)

- [Azure でキー コンテナーのキーを復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4:キー紛失のリスクを軽減する

| Azure ID | CIS コントロール v7.1 ID | NIST SP800-53 r4 ID |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

キーの紛失を回避および回復する手段を確保します。 Azure Key Vault で論理的な削除と消去保護を有効にして、キーが偶発的または悪意から削除されないようにします。  

- [Key Vault で論理的な削除と消去保護を有効にする方法](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [データ セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

