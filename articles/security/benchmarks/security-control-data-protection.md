---
title: Azure セキュリティ コントロール - データ保護
description: セキュリティ コントロール データ保護
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934518"
---
# <a name="security-control-data-protection"></a>セキュリティ コントロール:データ保護

データ保護の推奨事項では、暗号化、アクセス制御リスト、ID ベースのアクセス制御、およびデータ アクセスの監査ログに関連した問題の対処に重点を置いています。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.1 | 13.1 | Customer |

タグを使用して、機密情報を格納または処理する Azure リソースの追跡に役立てます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.2 | 13.2 | Customer |

開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、VNet/サブネットで分割し、適切にタグを付け、NSG または Azure Firewall で保護する必要があります。 機密データを格納または処理するリソースは、十分に分離する必要があります。 機密データを格納または処理する仮想マシンでは、使用されていないときにはオフにするためのポリシーとプロシージャを実装します。

追加の Azure サブスクリプションを作成する方法:

https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法:

https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

仮想ネットワークを作成する方法:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

セキュリティ構成を使用して NSG を作成する方法:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall をデプロイする方法:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Firewall でアラートまたはアラートと拒否を構成する方法:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.3 | 13.3 | Customer |

機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする自動ツールをネットワーク境界にデプロイします。

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.4. | 14.4 | 共有 |

転送中のすべての機密情報を暗号化します。 Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

Azure での転送中の暗号化について:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.5 | 14.5 | Customer |

Azure の特定のサービスで利用できる機能がない場合は、サードパーティのアクティブ検出ツールを使用して、組織のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報を更新します。

Office 365 ドキュメント内の機密情報を特定するには、Azure Information Protection を使用します。

Azure SQL Information Protection は、Azure SQL Database に格納されている情報の分類とラベル付けを支援するために使用します。

Azure SQL Data Discovery を実装する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Azure Information Protection を実装する方法:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.6 | 14.6 | Customer |

Azure AD RBAC を使用してデータとリソースへのアクセスを制御します。それ以外の場合は、サービス固有のアクセス制御方法を使用します。

Azure RBAC について:

https://docs.microsoft.com/azure/role-based-access-control/overview

Azure で RBAC を構成する方法:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.7 | 14.7 | Customer |

ホストベースの自動データ損失防止ソリューションなどのサードパーティ ツールを実装して、データがシステムからコピーされた場合でもデータへのアクセス制御を実施します。

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.8 | 14.8 | Customer |

すべての Azure リソースで保存時の暗号化を使用します。 Microsoft では、Azure に暗号化キーの管理を許可することをお勧めしていますが、一部のインスタンスでユーザーが自身のキーを管理するという選択肢もあります。 

Azure での保存時の暗号化について:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

ユーザーが管理する暗号化キーを構成する方法:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 4.9 | 14.9 | Customer |

Azure アクティビティ ログで Azure Monitor を使用して、重要な Azure リソースに変更が加えられたときのアラートを作成します。

Azure アクティビティ ログ イベントのアラートを作成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>次のステップ

次のセキュリティ コントロールを参照してください: [脆弱性の管理](security-control-vulnerability-management.md)
