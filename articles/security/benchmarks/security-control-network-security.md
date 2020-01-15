---
title: Azure セキュリティ コントロール - ネットワークのセキュリティ
description: セキュリティ コントロール、ネットワークのセキュリティ
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d052226470042d374544de0b7e1ced4ca0f48a14
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563957"
---
# <a name="security-control-network-security"></a>セキュリティ コントロール:ネットワークのセキュリティ

ネットワークのセキュリティに関する推奨事項では、Azure サービスへのアクセスを許可または拒否する対象のネットワーク プロトコル、TCP/UDP ポート、およびネットワーク接続済みサービスを指定することに重点を置いています。

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.1 | 9.2、9.4、14.1-14.3 | Customer |

すべての Virtual Network サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループがあることを確認します。 Private Link が有効になっている Azure サービスを使用するか、VNet 内にサービスをデプロイするか、プライベート エンドポイントを使用してプライベートに接続します。 サービス固有の要件については、その特定のサービスのセキュリティに関する推奨事項を参照してください。

または、特定のユース ケースがある場合は、Azure Firewall を実装することで要件を満たすことができます。

Private Link に関する一般情報: https://docs.microsoft.com/azure/private-link/private-link-overview

Virtual Network の作成方法:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

セキュリティ構成を使用して NSG を作成する方法:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall をデプロイして構成する方法:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.2 | 9.3、12.2 | Customer |

Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを保護します。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center によって提供されるネットワークのセキュリティの概要:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.3 | 9.5 | Customer |

着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。

Azure WAF をデプロイする方法:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.4 | 12.3 | Customer |

DDoS 攻撃から保護するために、Azure Virtual Network で DDoS Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある IP アドレスとの通信を拒否します。

有効化され、悪意のあるネットワーク トラフィックに対して &quot;アラートおよび拒否&quot; するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

Azure Security Center の Just In Time ネットワーク アクセスを使用して、NSG を構成し、限られた期間、承認された IP アドレスへのエンドポイントの公開を制限します。

Azure Security Center のアダプティブ ネットワークのセキュリティ強化を使用して、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨します。

DDoS 保護を構成する方法:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall をデプロイする方法:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Security Center の統合された脅威インテリジェンスの概要:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Azure Security Center のアダプティブ ネットワークのセキュリティ強化の概要

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center の Just In Time ネットワーク アクセス制御の概要

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.5 | 12.5、15.8 | Customer |

フロー レコードを生成するために、NSG フロー ログをストレージ アカウントに記録します。 異常なアクティビティを調査する必要がある場合は、Network Watcher パケット キャプチャを有効にします。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher を有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.6 | 12.6、12.7 | Customer |

有効化され、悪意のあるネットワーク トラフィックに対して &quot;アラートおよび拒否&quot; するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

Azure Firewall をデプロイする方法: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Firewall でアラートを構成する方法: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.7 | 12.9、12.10 | Customer |

信頼できる証明書に対して HTTPS/SSL を有効にした Web アプリケーションの Azure Application Gateway をデプロイします。

Application Gateway をデプロイする方法: https://docs.microsoft.com/azure/application-gateway/quick-create-portal

HTTPS を使用するように Application Gateway を構成する方法: https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散の概要: https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.8 | 1.5 | Customer |

Virtual Network サービス タグ&nbsp;を使用して、ネットワーク セキュリティ グループまたは Azure Firewall のネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

サービス タグの概要と使用方法: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.9 | 11.1 | Customer |

Azure Policy を使用して、ネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に適用し、バージョン管理によって制御と管理を微調整できます。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

ネットワークに関する Azure Policy のサンプル:

https://docs.microsoft.com/azure/governance/policy/samples/#network

Azure Blueprint の作成方法:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.1 | 11.2 | Customer |

ネットワークのセキュリティおよびトラフィック フローに関連する NSG およびその他のリソースにタグを使用します。 個々の NSG 規則については、&quot;[説明]&quot; フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network を作成する方法:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

セキュリティ構成を使用して NSG を作成する方法:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

| Azure ID | CIS ID | 担当 |
|--|--|--|
| 1.11 | 11.3 | Customer |

Azure Policy を使用して、ネットワーク リソースの構成の検証 (または修復、あるいはその両方) を行います。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

ネットワークに関する Azure Policy のサンプル:

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[ログ記録と監視](security-control-logging-monitoring.md)