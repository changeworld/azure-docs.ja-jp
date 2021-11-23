---
title: Microsoft Sentinel コンテンツ ハブ カタログ | Microsoft Docs
description: この記事では、現在提供されている Microsoft Sentinel コンテンツ ハブ パッケージと、その詳細を示します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b72ca149f2f61e297492da7c16d392c4d5f351e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711669"
---
# <a name="microsoft-sentinel-content-hub-catalog"></a>Microsoft Sentinel コンテンツ ハブ カタログ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft Sentinel コンテンツ ハブのエクスペリエンスは、現在 **プレビュー版** です。個別のソリューション パッケージもすべてプレビュー版です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[Microsoft Sentinel ソリューション](sentinel-solutions.md)は、データ コネクタ、ブック、分析、オートメーションといった Microsoft Sentinel のコンテンツを 1 回のデプロイ ステップでワークスペースに取得する、統合された方法を提供します。

この記事では、ワークスペースへのデプロイのために提供されている、Microsoft Sentinel のすぐに利用できる (組み込みの) オンデマンドのデータ コネクタとソリューションの一覧を示します。 ソリューションをデプロイすると、データ コネクタ、プレイブック、ブック、ルールなど、含まれているすべてのセキュリティ コンテンツが Microsoft Sentinel の関連領域に作成されます。 

詳細については、「[Microsoft Sentinel のすぐに使えるコンテンツとソリューションを一元的に検出してデプロイする](sentinel-solutions-deploy.md)」を参照してください。

## <a name="domain-solutions"></a>ドメイン ソリューション

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Microsoft インサイダー リスク管理** |[データ コネクタ](data-connectors-reference.md#microsoft-365-insider-risk-management-irm-preview)、ブック、分析ルール、ハンティング クエリ |セキュリティ - 内部関係者による脅威 | Microsoft|
| **Microsoft MITRE ATT&CK クラウド用ソリューション**| ブック、分析ルール、ハンティング クエリ|セキュリティ - 脅威からのデータ保護、セキュリティ - その他 |Microsoft |
| **Microsoft Sentinel Deception** | [ブック、分析ルール、ウォッチリスト](monitor-key-vault-honeytokens.md)  | セキュリティ - 脅威からのデータ保護  |Microsoft |
|**ゼロ トラスト** (TIC 3.0) |Workbooks |ID、セキュリティ - その他 |Microsoft |
| | | | |

## <a name="arista-networks"></a>Arista Networks

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Arista Networks** (Awake Security) |データ コネクタ、ブック、分析ルール | セキュリティ - ネットワーク |[Arista - Awake Security](https://awakesecurity.com/) |
| | | | |


## <a name="armorblox"></a>Armorblox

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Armorblox - Sentinel** |データ コネクタ | セキュリティ - 脅威からのデータ保護 |[Armorblox](https://www.armorblox.com/contact/) |
| | | | |




## <a name="azure"></a>Azure

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Azure Firewall Solution for Sentinel**| [データ コネクタ](data-connectors-reference.md#azure-firewall)、ブック、分析ルール、プレイブック、ハンティング クエリ、カスタム ロジック アプリ コネクタ |セキュリティ - ネットワーク セキュリティ、ネットワーク | コミュニティ|
|**SQL PaaS 用 Microsoft Sentinel**     |  [データ コネクタ](data-connectors-reference.md#azure-sql-databases)、ブック、分析ルール、プレイブック、ハンティング クエリ     | Application        |      コミュニティ   |
|**Microsoft Sentinel トレーニング ラボ** |ブック、分析ルール、プレイブック、ハンティング クエリ | トレーニングとチュートリアル |Microsoft |
|**Azure SQL** | [データ コネクタ](data-connectors-reference.md#azure-sql-databases)、ブック、分析、プレイブック、ハンティング クエリ  | Application |Microsoft  |
| | | | |


## <a name="box"></a>ボックス

|名前   |Includes  |Categories |サポートしているもの  |
|------------------|---------|---------|---------|
|**Box ソリューション**| データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー |  ストレージ、アプリケーション  | Microsoft|
| | | | |


## <a name="check-point"></a>Check Point

|名前   |Includes  |Categories |サポートしているもの  |
|------------------|---------|---------|---------|
|**Check Point Microsoft Sentinel ソリューション**   |[データ コネクタ](data-connectors-reference.md#check-point)、プレイブック、カスタム ロジック アプリ コネクタ  | セキュリティ - Automation (SOAR) | [チェックポイント](https://www.checkpoint.com/support-services/contact-support/)|
| | | | |


## <a name="cisco"></a>Cisco

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Cisco ACI** |データ コネクタ、パーサー |セキュリティ - ネットワーク |Microsoft |
|**Cisco ASA** |[データ コネクタ](data-connectors-reference.md#cisco-asa)、プレイブック、カスタム ロジック アプリ コネクタ |セキュリティ - Automation (SOAR) |Microsoft |
|**Cisco Duo セキュリティ** |データ コネクタ、パーサー | ID|Microsoft |
|**Cisco ISE**  |データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、カスタム ロジック アプリ コネクタ |ネットワーク、セキュリティ - その他 | Microsoft |
|**Cisco Meraki** |[データ コネクタ](data-connectors-reference.md#cisco-meraki-preview)、プレイブック、カスタム ロジック アプリ コネクタ |セキュリティ - ネットワーク |Microsoft |
|**Cisco Secure Email ゲートウェイ/ESA** |データ コネクタ、パーサー |セキュリティ - 脅威からのデータ保護 |Microsoft |
|**Cisco StealthWatch** |データ コネクタ、パーサー |セキュリティ - ネットワーク | Microsoft|
|**Cisco Umbrella** |[データ コネクタ](data-connectors-reference.md#cisco-umbrella-preview)、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、カスタム ロジック アプリ コネクタ |セキュリティ - クラウド セキュリティ |Microsoft |
|**Cisco Web Security Appliance (WSA)** | データ コネクタ、パーサー|セキュリティ - ネットワーク |Microsoft |
| | | | |


## <a name="cloudflare"></a>Cloudflare


|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Cloudflare ソリューション**|データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー| セキュリティ - ネットワーク |Microsoft |
| | | | |


## <a name="contrast-security"></a>Contrast Security



|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Contrast Protect Microsoft Sentinel ソリューション**|データ コネクタ、ブック、分析ルール |セキュリティ - 脅威からのデータ保護 |Microsoft  |
| | | | |

## <a name="crowdstrike"></a>Crowdstrike


|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**CrowdStrike Falcon Endpoint Protection ソリューション**| データ コネクタ、ブック、分析ルール、プレイブック、パーサー| セキュリティ - 脅威からのデータ保護| Microsoft|
| | | | |

## <a name="digital-guardian"></a>Digital Guardian


|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Digital Guardian** |データ コネクタ、パーサー |セキュリティ - Information Protection |Microsoft |
| | | |

## <a name="falconforce"></a>FalconForce

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**FalconFriday Content - Falcon Friday** |分析ルール |ユーザーの行動 (UEBA)、セキュリティ - 内部関係者による脅威 | [FalconForce](https://www.falconforce.nl/en/)|
| | | |

## <a name="fireeye-nx-network-security"></a>FireEye NX (ネットワーク セキュリティ)

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**FireEye NX (ネットワーク セキュリティ)** |データ コネクタ、パーサー |セキュリティ - ネットワーク| Microsoft|
| | | |

## <a name="flare-systems-firework"></a>Flare Systems Firework

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Flare Systems Firework** |データ コネクタ |セキュリティ - 脅威からのデータ保護 |Microsoft|
| | | |

## <a name="forescout"></a>Forescout

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Forescout** |データ コネクタ、パーサー |セキュリティ - ネットワーク | Microsoft|
| | | |

## <a name="fortinet-fortigate"></a>Fortinet Fortigate

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Fortinet Fortigate** |[データ コネクタ](data-connectors-reference.md#fortinet)、プレイブック、カスタム ロジック アプリ コネクタ|セキュリティ - Automation (SOAR) | Microsoft|
| | | |


## <a name="google"></a>Google

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Google Cloud Platform DNS ソリューション** |データ コネクタ、パーサー |クラウド プロバイダー、ネットワーク |Microsoft |
|**Google Cloud Platform Cloud Monitoring ソリューション**|データ コネクタ、パーサー |クラウド プロバイダー | Microsoft|
|**Google Cloud Platform Identity and Access Management ソリューション**|データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、カスタム ロジック アプリ コネクタ|クラウド プロバイダー、ID |Microsoft |
| | | | |


## <a name="hyas"></a>HYAS

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**HYAS Insight for Microsoft Sentinel ソリューション ギャラリー**| プレイブック| セキュリティ - 脅威インテリジェンス、セキュリティ - Automation (SOAR) |Microsoft |
| | | | |

## <a name="imperva"></a>Imperva

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Imperva Cloud WAF** (以前の Imperva Incapsula)| [データ コネクタ](data-connectors-reference.md#imperva-waf-gateway-preview)、パーサー| セキュリティ - ネットワーク | Microsoft|
| | | | |

## <a name="infoblox"></a>InfoBlox

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**InfoBlox Threat Defense/InfoBlox Cloud Data Connector**| [データ コネクタ](data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)、ブック、分析ルール| セキュリティ - 脅威からのデータ保護 | Microsoft|
| | | | |


## <a name="ironnet"></a>IronNet

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**IronNet CyberSecurity Iron Defense - Microsoft Sentinel** | |セキュリティ - ネットワーク |Microsoft |
| | | |



## <a name="juniper"></a>Juniper

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Juniper IDP** |データ コネクタ、パーサー|セキュリティ - ネットワーク |Microsoft |
| | | | |


## <a name="kaspersky"></a>Kaspersky

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Kaspersky AntiVirus** |データ コネクタ、パーサー   | セキュリティ - 脅威からのデータ保護|Microsoft |
| | | | |


## <a name="lookout"></a>Lookout

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Microsoft Sentinel 用 Lookout Mobile Threat Defense**| [データ コネクタ](data-connectors-reference.md#lookout-mobile-threat-defense-preview)|セキュリティ - ネットワーク |[Lookout](https://www.lookout.com/support) |
| | | |

## <a name="mcafee"></a>McAfee

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**McAfee ePolicy Orchestrator ソリューション**| データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、カスタム ロジック アプリ コネクタ| セキュリティ - 脅威からのデータ保護| Microsoft |
|**McAfee Network Security Platform ソリューション** (Intrushield) + ウイルス対策情報 (T1 - ロジック アプリ) |データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー |セキュリティ - 脅威からのデータ保護 | Microsoft|
| | | | |

## <a name="microsoft"></a>Microsoft

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Microsoft Sentinel 4 Microsoft Dynamics 365**     |   [データ コネクタ](data-connectors-reference.md#dynamics-365)、ブック、分析ルール、ハンティング クエリ |      Application   |Microsoft         |
|**Teams 用 Microsoft Sentinel**     | データ コネクタ、分析ルール、プレイブック、ハンティング クエリ      |   Application      |    コミュニティ     |
| | | | |


## <a name="oracle"></a>Oracle


|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Oracle Cloud Infrastructure** |データ コネクタ、パーサー | クラウド プロバイダー | Microsoft|
|**Oracle Database Audit ソリューション** | データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー| Application|Microsoft |
| | | | |

## <a name="palo-alto"></a>Palo Alto

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Palo Alto PAN-OS**|[データ コネクタ](#palo-alto)、プレイブック、カスタム ロジック アプリ コネクタ |セキュリティ - Automation (SOAR)、セキュリティ - ネットワーク |Microsoft |
|**Palo Alto Prisma ソリューション**|[データ コネクタ](#palo-alto)、ブック、分析ルール、ハンティング クエリ、パーサー |セキュリティ - クラウド セキュリティ |Microsoft |
| | | | |

## <a name="ping-identity"></a>Ping Identity

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**PingFederate ソリューション** |データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー| ID|Microsoft |
| | | | |

## <a name="proofpoint"></a>Proofpoint

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Proofpoint POD ソリューション** |[データ コネクタ](data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)、ブック、分析ルール、ハンティング クエリ、パーサー| セキュリティ - 脅威からのデータ保護|Microsoft |
|**Proofpoint TAP ソリューション** | ブック、分析ルール、プレイブック、カスタム ロジック アプリ コネクター|セキュリティ - Automation (SOAR)、セキュリティ - 脅威からのデータ保護 |Microsoft |
| | | |

## <a name="qualys"></a>Qualys

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Qualys VM ソリューション** |ブック、分析ルール |セキュリティ - 脆弱性管理 |Microsoft |
| | | | |

## <a name="rapid7"></a>Rapid7

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Rapid7 InsightVM CloudAPI ソリューション** |データ コネクタ、パーサー|セキュリティ - 脆弱性管理 |Microsoft |
| | | | |

## <a name="reversinglabs"></a>ReversingLabs

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**ReversingLabs TitaniumCloud File Enrichment ソリューション**|プレイブック |セキュリティ - 脅威インテリジェンス |[ReversingLabs](https://support.reversinglabs.com/hc/en-us) |
| | | | |

## <a name="riskiq"></a>RiskIQ

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**RiskIQ セキュリティ インテリジェンス プレイブック**|プレイブック |セキュリティ - 脅威インテリジェンス、セキュリティ - Automation (SOAR) |[RiskIQ](https://www.riskiq.com/integrations/microsoft/) |
| | | | |

## <a name="rsa"></a>RSA

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**RSA SecurID** |データ コネクタ、パーサー |セキュリティ - その他、ID |Microsoft |
| | | |



## <a name="sap"></a>SAP

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Continuous Threat Monitoring for SAP**|[データ コネクタ](sap-deploy-solution.md)、[ブック、分析ルール、ウォッチリスト](sap-solution-security-content.md) | Application  |コミュニティ |
| | | | |

## <a name="semperis"></a>Semperis

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Semperis**|データ コネクタ、ブック、分析ルール、パーサー | セキュリティ - 脅威からのデータ保護、ID  |[Semperis](https://www.semperis.com/contact-us/) |
| | | | |

## <a name="senserva-pro"></a>Senserva Pro

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Senserva Offer for Microsoft Sentinel** |データ コネクタ、ブック、分析ルール、ハンティング クエリ |コンプライアンス |[Senserva](https://www.senserva.com/contact/) |
| | | | |


## <a name="sonrai-security"></a>Sonrai Security

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Sonrai Security - Microsoft Sentinel** |データ コネクタ、ブック、分析ルール   | コンプライアンス|Sonrai Security |
| | | | |

## <a name="slack"></a>Slack

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Slack Audit ソリューション**|データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー |Application| Microsoft|
| | | | |


## <a name="sophos"></a>Sophos

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Sophos Endpoint Protection ソリューション** |データ コネクタ、パーサー| セキュリティ - 脅威からのデータ保護 |Microsoft |
|**Sophos XG Firewall ソリューション**| ブック、分析ルール、パーサー |セキュリティ - ネットワーク |Microsoft |
| | | | |


## <a name="symantec"></a>Symantec

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Symantec Endpoint**|データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー| セキュリティ - 脅威からのデータ保護|Microsoft |
|**Symantec ProxySG ソリューション**|ブック、分析ルール |セキュリティ - ネットワーク |Symantec |
| | | | |

## <a name="tenable"></a>Tenable

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Tenable Nessus スキャナー/クラウド用 IO VM レポート**  | データ コネクタ、パーサー| セキュリティ - 脆弱性管理| Microsoft |
| | | | |


## <a name="trend-micro"></a>Trend Micro

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Trend Micro Apex One ソリューション**  | データ コネクタ、ハンティング クエリ、パーサー| セキュリティ - 脅威からのデータ保護|Microsoft |
| | | | |




## <a name="ubiquiti"></a>Ubiquiti

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Ubiquiti UniFi ソリューション**|データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー |セキュリティ - ネットワーク |Microsoft |
| | | | |


## <a name="varmour"></a>vArmour

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**vArmour Application Controller と Microsoft Sentinel のソリューション**|データ コネクタ、ブック、分析ルール |IT 運用 |[vArmour](https://www.varmour.com/contact-us/) |
| | | | |

## <a name="vectra"></a>Vectra

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Vectra Stream ソリューション** |データ コネクタ、ハンティング クエリ、パーサー |セキュリティ - ネットワーク |Microsoft |
| | | |


## <a name="vmware"></a>VMware

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**VMware Carbon Black ソリューション**|ブック、分析ルール| セキュリティ - 脅威からのデータ保護| Microsoft|
| | | | |

## <a name="zeek-network"></a>Zeek Network

|名前    |Includes  |Categories |サポートしているもの  |
|---------|---------|---------|---------|
|**Corelight for Microsoft Sentinel**|データ コネクタ、ブック、分析ルール、ハンティング クエリ、パーサー | IT 運用、セキュリティ - ネットワーク | [Zeek Network](https://support.corelight.com/)|
| | | | |


## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Sentinel ソリューションと、それを見つけてデプロイする方法について説明しました。

- [Microsoft Sentinel ソリューション](sentinel-solutions.md)に関する詳細を確認します。
- [Microsoft Azure Sentinel ソリューションを見つけて展開します](sentinel-solutions-deploy.md)。
