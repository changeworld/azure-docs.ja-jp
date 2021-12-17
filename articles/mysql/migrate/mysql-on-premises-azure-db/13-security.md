---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: セキュリティ'
description: クラウドベースのサービスに移行しても、インターネット全体が必ずしもそれにアクセスできるとは限りません。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: f0d0613dca258050cec75d544438b781c1f9fe96
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285233"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-security"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する: セキュリティ

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[事業継続とディザスター リカバリー](12-business-continuity-and-disaster-recovery.md)

## <a name="overview"></a>概要

クラウドベースのサービスに移行しても、インターネット全体が必ずしもそれにアクセスできるとは限りません。 Azure では、不正ユーザーや悪意のあるプログラムからデータ ワークロードを継続的に保護できるクラス最高のセキュリティを提供します。

## <a name="authentication"></a>認証

Azure Database for MySQL では、MySQL ユーザーの接続で基本認証メカニズムがサポートされていますが、[Azure Active Directory との統合](../../concepts-azure-ad-authentication.md)もサポートされています。 このセキュリティ統合は、MySQL ログイン プロセス中にパスワードのような機能を果たすトークンを発行することで機能します。 [Active Directory 統合の構成](../../howto-configure-sign-in-azure-ad-authentication.md)は非常に簡単であり、ユーザーだけでなく、AAD グループもサポートされます。

この緊密な統合により、管理者とアプリケーションは、[Azure Identity Protection](../../../active-directory/identity-protection/overview-identity-protection.md) の強化されたセキュリティ機能を利用して、ID 関連の問題をはっきりと明るみに出すことができます。

> [!NOTE] 
> このセキュリティ機能は、MySQL 5.7 以降でサポートされています。 `clear-text` オプションが指定されていれば、ほとんどの[アプリケーション ドライバー](../../howto-configure-sign-in-azure-ad-authentication.md)がサポートされます。

## <a name="threat-protection"></a>脅威の防止

ユーザーまたはアプリケーションの資格情報が漏洩すると、ログインの試行失敗に関する情報がログに表示されなくなる可能性があります。 資格情報が漏洩すると、不正ユーザーがデータにアクセスしてダウンロードできるようになる可能性があります。 [Azure Threat Protection](../../concepts-security.md#threat-protection) では、ログインの異常 (通常とは異なる場所、まれなユーザー、ブルート フォース攻撃など) をはじめとする疑わしいアクティビティを監視できます。 管理者は、問題が発生した場合に通知を受け取ることができます。

## <a name="audit-logging"></a>監査ログ

MySQL には、堅牢な監査ログ機能が組み込まれています。 既定では、Azure Database for MySQL でこの[監査ログ機能が無効](../../concepts-audit-logs.md)になっています。 サーバー レベルのログは、`audit\_log\_enabled` サーバー パラメーターを変更することで有効にすることができます。 有効にしたら、[診断ログ](../../howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)を有効にすることで、[Azure Monitor](../../../azure-monitor/overview.md) と [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) からログにアクセスできます。

ユーザー接続関連のイベントを照会するには、次の KQL クエリを実行します。

```
AzureDiagnostics  
| where ResourceProvider =="MICROSOFT.DBFORMYSQL" 
| where Category == 'MySqlAuditLogs' and event\_class\_s == "connection\_log"  
| project TimeGenerated, LogicalServerName\_s, event\_class\_s, event\_subclass\_s  
, event\_time\_t, user\_s , ip\_s , sql\_text\_s  
| order by TimeGenerated asc
```

## <a name="encryption"></a>暗号化

MySQL インスタンス内のデータは、既定では保存時に暗号化されます。 また、承認されていない関係者へのデータ漏洩の可能性を防ぐために、自動バックアップも暗号化されます。 通常、この暗号化は、インスタンスの作成時に作成されるキーを使用して実行されます。 この既定の暗号化キーに加えて、管理者は [Bring Your Own Key (BYOK)](../../concepts-data-encryption-mysql.md) を使用することもできます。

カスタマー マネージド キー戦略を使用する場合、キー ライフサイクル管理に関する責任を理解しておくことが重要です。 カスタマー キーは [Azure Key Vault](../../../key-vault/general/basic-concepts.md) に格納され、ポリシーを介してアクセスされます。 キー管理に関するすべての推奨事項に従うことが重要です。暗号化キーが失われると、データ アクセスが失われます。

カスタマー マネージド キーに加えて、サービスレベルのキーを使用して[二重暗号化を追加](../../concepts-infrastructure-double-encryption.md)します。 この機能を実装すると、保存データを高度に暗号化できますが、暗号化のパフォーマンスが低下します。 テストを実施することをお勧めします。

SSL/TLS を使用すると、転送中のデータを暗号化できます。 前に説明したように、この変更をサポートするために[アプリケーションを変更](../../howto-configure-ssl.md)し、適切な TLS 検証設定を構成することが必要になる場合があります。

## <a name="firewall"></a>ファイアウォール

ユーザーが設定され、データが保存時に暗号化されたら、移行チームはネットワーク データ フローを確認する必要があります。 Azure Database for MySQL には、許可されているユーザー、アプリケーション、デバイスだけにアクセスを制限してネットワーク レイヤーをセキュリティで保護するメカニズムがいくつか用意されています。

MySQL インスタンスを保護するための防御の最前線は、[ファイアウォール規則](../../concepts-firewall-rules.md)の実装です。 内部または外部 IP を介してインスタンスにアクセスする場合、IP アドレスを有効な場所だけに制限できます。 MySQL インスタンスが内部アプリケーションにのみサービスを提供することになっている場合は、[パブリック アクセスを制限](../../howto-deny-public-network-access.md)します。

アプリケーションを MySQL ワークロードと共に Azure に移行する場合、複数の仮想ネットワークがハブ アンド スポーク パターンで設定されており、[仮想ネットワーク ピアリング](../../../virtual-network/virtual-network-peering-overview.md)の構成が必要になる可能性があります。

## <a name="private-link"></a>プライベート リンク

Azure Database for MySQL へのアクセスを内部 Azure リソースに制限するには、[Private Link](../../concepts-data-access-security-private-link.md) を有効にします。 Private Link を使用すると、MySQL インスタンスに、パブリック IP アドレスではなくプライベート IP を割り当てることができます。

> [!NOTE]
> このガイドでは取り上げていませんが、考慮する必要がある、[Azure ネットワークに関する基本的な考慮事項](../../concepts-data-access-and-security-vnet.md)がほかにも多数あります。

すべての Azure リソースで実践できる可能性のある一連の[セキュリティ ベースライン](/azure/mysql/security-baseline) タスクを確認します。 参照リンクに記載されている項目は、必ずしもすべてが特定のデータ ワークロードまたは Azure リソースに適用されるわけではありません。

## <a name="security-checklist"></a>セキュリティ チェックリスト

  - 可能であれば、Azure AD 認証を使用します。

  - Advanced Thread Protection を有効にします。

  - すべての監査機能を有効にします。

  - Bring-Your-Own-Key (BYOK) 戦略を検討します。

  - ファイアウォール規則を実装します。

  - インターネットを経由しないワークロードには、プライベート エンドポイントを使用します。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [まとめ](./14-summary.md)