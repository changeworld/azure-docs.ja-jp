---
title: Azure サービスのセキュリティ属性
description: Azure Service Fabric を評価するための一般的なセキュリティ属性のチェックリスト
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474542"
---
# <a name="security-attributes-for-azure-services"></a>Azure サービスのセキュリティ属性

この記事では、特定の Azure サービスについて、その一般的なセキュリティ属性をまとめています。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい (サービス側の暗号化のみ) | 証明書、キー、およびシークレットという名前付きの値などの機密データは、サービスで管理される、サービス インスタンスごとのキーで暗号化されます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | [Express Route](../expressroute/index.yml) と VNet 暗号化は、[Azure ネットワーク](../virtual-network/index.yml)によって提供されます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | すべての暗号化キーはサービス インスタンスごとに存在し、サービスで管理されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | 管理プレーン呼び出しは、TLS 経由で [Azure Resource Manager](../azure-resource-manager/index.yml) によって行われます。 有効な JSON Web トークン (JWT) が必要です。  データ プレーン呼び出しは、TLS と、サポートされる認証メカニズムのいずれか (クライアント証明書や JWT など) で保護できます。
 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい | それぞれ、ネットワーク セキュリティ グループ (NSG) と Azure Application Gateway (またはその他のソフトウェア アプライアンス) を使用します。 |
| 強制トンネリングのサポート| はい | Azure ネットワークでは、強制トンネリングを提供します。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | |
| Authorization| はい | |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Monitor のアクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md) |
| データ プレーンのログ記録と監査| はい | [Azure Monitor 診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)と (必要に応じて) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure API Management DevOps リソース キット](https://aka.ms/apimdevops)を使用 |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | Web サイト ファイルのコンテンツは、Azure Storage に格納されます。これにより、コンテンツは保存時に自動的に暗号化されます。 「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」を参照してください。<br><br>お客様が提供するシークレットは保存時に暗号化されます。 シークレットは、App Service 構成データベースに格納されている間、保存時に暗号化されます。<br><br>ローカルに接続されたディスクは、必要に応じて、Web サイトによって一時ストレージとして使用できます (D:\local および %TMP%)。 ローカルに接続されたディスクでは、保存時の暗号化は行われません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | お客様は、インバウンド トラフィックで HTTPS を必要とし、使用するように Web サイトを構成することができます。 ブログ記事の「[How to make an Azure App Service HTTPS only (Azure App Service を HTTPS のみにする方法)](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)」を参照してください。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | お客様はアプリケーション シークレットを Key Vault に格納し、それらを実行時に取得することを選択できます。 「[App Service と Azure Functions の Key Vault 参照を使用する (プレビュー)](../app-service/app-service-key-vault-references.md)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | App Service を構成するための管理呼び出しは、HTTPS 経由の [Azure Resource Manager](../azure-resource-manager/index.yml) 呼び出しで行われます。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | 現在、App Service のプレビューで利用可能です。 [Azure App Service のアクセス制限](../app-service/app-service-ip-restrictions.md)に関するページを参照してください。 |
| VNet インジェクションのサポート| はい | App Service Environment は、お客様の仮想ネットワークに挿入される 1 人のお客様専用の App Service のプライベート実装です。 「[App Service 環境の概要](../app-service/environment/intro.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | App Service のパブリック マルチテナント バリエーションでは、お客様は、許可されたインバウンド トラフィックをロック ダウンするようにネットワーク ACL (IP 制限) を構成することができます。  [Azure App Service のアクセス制限](../app-service/app-service-ip-restrictions.md)に関するページを参照してください。  App Service Environment は仮想ネットワークに直接デプロイされるため、NSG で保護することができます。 |
| 強制トンネリングのサポート| はい | App Service Environment は、強制トンネルが構成された、お客様の仮想ネットワークにデプロイすることができます。 お客様は、「[強制トンネリングを使用した App Service Environment の構成](../app-service/environment/forced-tunnel-support.md)」の指示に従う必要があります。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | App Service は、Application Insights をサポートする言語用の Application Insights と統合されます (完全な .NET Framework、.NET Core、Java および Node.JS)。  「[Azure App Service のパフォーマンスの監視](../azure-monitor/app/azure-web-apps.md)」を参照してください。 App Service では、アプリケーション メトリックを Azure Monitor に送信することもできます。 「[Azure App Service のアプリの監視](../app-service/web-sites-monitor.md)」を参照してください。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | お客様は、[Azure Active Directory (Azure AD)](../active-directory/index.yml) および他の OAuth と互換性のある ID プロバイダーと自動的に統合される、App Service にアプリケーションをビルドすることができます (「[Azure App Service での認証および許可](../app-service/overview-authentication-authorization.md)」を参照)。 App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。 |
| Authorization| はい | App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。  |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | App Service オブジェクトで実行されるすべての管理操作は、[Azure Resource Manager](../azure-resource-manager/index.yml) 経由で実行されます。 これらの操作の履歴ログはポータルと CLI の両方を使用して入手できます (「[Azure Resource Manager リソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)」と「[az monitor activity-log](/cli/azure/monitor/activity-log)」を参照)。 |
| データ プレーンのログ記録と監査 | いいえ | App Service のデータ プレーンは、お客様のデプロイされた Web サイトのコンテンツを含む、リモート ファイル共有です。  リモート ファイル共有の監査はありません。 |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、App Service 構成の状態を Azure Resource Manager テンプレートとしてエクスポートし、経時的にバージョン管理できます。 ランタイム操作では、お客様は App Service デプロイ スロット機能を使用して、複数の異なるライブ バージョンのアプリケーションを維持できます。 | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい |  |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | HTTPS/TLS。 |
| 暗号化キーの処理 (CMK や BYOK など)| 該当なし | Azure Resource Manager では、制御データのみが格納され、お客様のコンテンツは格納されません。 |
| 列レベルの暗号化 (Azure Data Services)| はい | |
| API 呼び出しの暗号化| はい | |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| いいえ | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | [Azure Active Directory](/azure/active-directory) ベース。|
| Authorization| はい | |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | アクティビティ ログでは、リソースに対して実行された書き込み操作 (PUT、POST、DELETE) がすべて公開されます。「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/resource-group-audit.md)」を参照してください。 |
| データ プレーンのログ記録と監査| 該当なし | |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| いいえ | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ |  |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、診断ログを通じてサポートされます。 詳細については、Log Analytics を使用した Azure Backup で保護されているワークロードの監視に関するページ (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) ) を参照してください。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | カスタマーが作成した RBAC ロールとビルトインの RBAC ロールが使用されます。 詳細については、ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理に関するページ (/azure/backup/backup-rbac-rs-vault) を参照してください。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |
| データ プレーンのログ記録と監査| いいえ | Azure Backup データ プレーンには、直接アクセスできません。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | すべてのオブジェクトが暗号化されます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | すべての通信が暗号化された API 呼び出しを介して行われます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーが Key Vault のすべてのキーを制御します。 ハードウェア セキュリティ モジュール (HSM) バックアップ キーが指定されている場合、FIPS レベル 2 HSM により、キー、証明書、またはシークレットが保護されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | HTTPS を使用します。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | Virtual Network (VNet) サービス エンドポイントを使用。 |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VNet ファイアウォール規則を使用。 |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics を使用します。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | Key Vault アクセス ポリシーを使用します。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | Log Analytics を使用します。 |
| データ プレーンのログ記録と監査| はい | Log Analytics を使用します。 |

### <a name="access-controls"></a>アクセス制御

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 制御/管理プレーンのアクセス制御 | はい | Azure Resource Manager のロールベースのアクセス制御 (RBAC) |
| データ プレーンのアクセス制御 (すべてのサービス レベル) | はい | Key Vault アクセス ポリシー |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure Service Bus Messaging](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>|  はい (サーバー側の保存時の暗号化では既定で)。 | 顧客管理キーと BYOK はまだサポートされていません。 クライアント側の暗号化はクライアントの責任です |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | 標準の HTTPS/TLS メカニズムがサポートされます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |   |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | API 呼び出しは、[Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用して行われます。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい (Premium レベルのみ) | VNet サービス エンドポイントは、[Service Bus Premium レベル](../service-bus-messaging/service-bus-premium-messaging.md)でのみサポートされます。 |
| VNet インジェクションのサポート| いいえ | |
| ネットワークの分離とファイアウォールのサポート| はい (Premium レベルのみ) |  |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor とアラート](../service-bus-messaging/service-bus-metrics-azure-monitor.md)によってサポートされます。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | [Azure Active Directory マネージド サービス ID](../service-bus-messaging/service-bus-managed-service-identity.md)を使用して管理されます (「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照)。|
| Authorization| はい | [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (プレビュー) と SAS トークンによる承認がサポートされます (「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照)。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | 操作ログを使用できます (「[Service Bus 診断ログ](../service-bus-messaging/service-bus-diagnostic-logs.md)」を参照)。  |
| データ プレーンのログ記録と監査| いいえ |  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager API](/rest/api/resources/) によるリソース プロバイダーのバージョン管理がサポートされます。|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>|  該当なし | Relay は Web ソケットであり、データは保持されません。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | Service には TLS が必要です。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | Microsoft の TLS 証明書のみを使用します。  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| 該当なし | Relay は TLS トンネルです  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | SAS を使用。 |
| Authorization|  はい | SAS を使用。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |
| データ プレーンのログ記録と監査| はい | 接続の成功/失敗およびエラーはログに記録されます。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン (VM) スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい |  |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン (VM) スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | Service Fabric API は Azure Resource Manager によって呼び出されます。 有効な JSON Web トークン (JWT) が必要です。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい |  |
| ネットワークの分離とファイアウォールのサポート| はい | ネットワーク セキュリティ グループ (NSG) の使用。 |
| 強制トンネリングのサポート| はい | Azure ネットワークでは、強制トンネリングを提供します。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure 監視サポートとサード パーティ サポートの使用。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | SFRP 経由の呼び出しのための ID およびアクセス管理 (IAM)。 クラスター エンド ポイントは、ユーザーと管理者という 2 つのロールで直接呼び出すことができます。ユーザーは API をいずれかのロールにマッピングできます。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | コントロール プレーン操作はすべて、監査と承認のプロセスを経由して実行されます。 |
| データ プレーンのログ記録と監査| 該当なし | ユーザーはクラスターを所有します。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | サービス構成は Azure Deploy によってバージョン管理され、デプロイされます。 コード (アプリケーションとランタイム) は Azure Build によってバージョン管理されます。
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | [Always Encrypted](../sql-database/sql-database-always-encrypted.md) に関する記事で説明されているように、"使用時の暗号化" と呼ばれます。 サービス側暗号化では、[Transparent Data Encryption](../sql-database/transparent-data-encryption-azure-sql.md) (TDE) が使われます。|
| 転送中の暗号化:<ul><li>ExpressRoute の暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | サービス マネージド キーとカスタマー マネージド キーの処理がどちらも提供されます (後者は を[Azure Key Vault](../key-vault/index.yml) によって)。 |
| 列レベルの暗号化 (Azure Data Services)| はい | [Always Encrypted](../sql-database/sql-database-always-encrypted.md) により。 |
| API 呼び出しの暗号化| はい | HTTPS/SSL を使用。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | [単一データベース](../sql-database/sql-database-single-index.yml)のみに適用。 |
| VNet インジェクションのサポート| はい | [マネージド インスタンス](../sql-database/sql-database-managed-instance.md)のみに適用。 |
| ネットワークの分離とファイアウォールのサポート| はい | データベース レベルとサーバー レベル両方でのファイアウォール、[マネージド インスタンス](../sql-database/sql-database-managed-instance.md)のみに対するネットワーク分離 |
| 強制トンネリングのサポート| はい | [Azure ExpressRoute](../expressroute/index.yml) VPN による[マネージド インスタンス](../sql-database/sql-database-managed-instance.md) |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [SQL 監査](../sql-database/sql-database-auditing.md)を介した [Azure Event Hubs](../event-hubs/index.yml) の統合により、Imperva (SecureSphere) からのサード パーティーの SIEM ソリューションもサポートされます。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Azure Active Directory。 |
| Authorization| はい |  |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | 一部のイベントについてのみ、はい。 |
| データ プレーンのログ記録と監査 | はい | [SQL 監査](../sql-database/sql-database-auditing.md)により。 |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | | 

### <a name="additional-security-attributes-for-sql-database"></a>SQL Database の追加セキュリティ属性

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 予防: 脆弱性評価 | はい | 「[SQL の脆弱性評価サービスは、データベースの脆弱性を特定するのに役に立ちます](../sql-database/sql-vulnerability-assessment.md)」をご覧ください。 |
| 予防: データの検出と分類  | はい | 「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](../sql-database/sql-database-data-discovery-and-classification.md)」をご覧ください。 |
| 検出: 脅威の検出 | はい | 「[Azure SQL Database の Advanced Threat Protection](../sql-database/sql-database-threat-detection-overview.md)」をご覧ください。 |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい |  |
| 転送中の暗号化:<ul><li>Express Route 暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | 標準の HTTPS/TLS 機構をサポートします。  サービスに転送するデータは、ユーザーが事前に暗号化することもできます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | 「[Azure Key Vault で顧客が管理するキーを Storage Service Encryption に使用する](../storage/common/storage-service-encryption-customer-managed-keys.md)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| 該当なし |  |
| ネットワークの分離とファイアウォールのサポート| はい | |
| 強制トンネリングのサポート| 該当なし |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Monitor のメトリックが利用できるようになり、ログのプレビューが開始されました |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Azure Active Directory、共有キー、共有アクセス トークン。 |
| Authorization| はい | RBAC、POSIX ACL、SAS トークンを使用した承認をサポートします |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査 | はい | Azure Resource Manager アクティビティ ログ |
| データ プレーンのログ記録と監査| はい | サービスの診断ログおよび Azure Monitor のログのプレビューが開始されました  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | Azure Resource Manager API によりリソース プロバイダーのバージョン管理をサポートします |