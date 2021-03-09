---
title: Azure RTOS 用のセキュリティ モジュールを構成およびカスタマイズする
description: Azure RTOS 用のセキュリティ モジュールを構成およびカスタマイズする方法について説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: fb2b7810c0829859f4a104c62b6df2ca0495bac7
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809203"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Azure RTOS のセキュリティ モジュールを構成およびカスタマイズする (プレビュー)

デバイスの動作を構成するには、次のファイルを使用します。

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/inc/asc_port.h

 各構成の既定の動作を次の表に示します。 

### <a name="general"></a>全般

| 名前 | 種類 | Default | 詳細 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | --- | デバイスの一意識別子  |
| ASC_SECURITY_MODULE_PENDING_TIME  | 番号 | 300 | セキュリティ モジュールの保留時間 (秒)。 時間が超過した場合、状態が中断に変わります。 |

#### <a name="collection"></a>コレクション

| 名前 | 種類 | Default | 詳細 |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | 番号 | 10 | コレクターの優先度が高いグループ間隔 (秒)。 |
| ASC_MEDIUM_PRIORITY_INTERVAL | 番号 | 30 | コレクターの中程度の優先度のグループ間隔 (秒)。 |
| ASC_LOW_PRIORITY_INTERVAL | 番号 | 145,440  | コレクターの低優先度のグループ間隔 (秒)。 |

#### <a name="collector-network-activity"></a>コレクターのネットワーク アクティビティ

コレクターのネットワーク アクティビティ構成をカスタマイズするには、以下を使用します。

| 名前 | 種類 | Default | 詳細 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | `TCP` ネットワーク アクティビティをフィルター処理します |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | `UDP` ネットワーク アクティビティ イベントをフィルター処理します |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | `ICMP` ネットワーク アクティビティ イベントをフィルター処理します |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | ユニキャスト受信パケットのみをキャプチャします。false に設定した場合は、ブロードキャストとマルチキャストもキャプチャします |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | 番号 | 64 | メモリに格納する IPv4 ネットワーク イベントの最大数 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | 番号 | 64  | メモリに格納する IPv6 ネットワーク イベントの最大数 |


## <a name="compile-flags"></a>コンパイル フラグ
コンパイル フラグを使用すると、定義済みの構成をオーバーライドできます。

### <a name="collectors"></a>Collectors
| 名前 | 種類 | Default | 詳細 |
| - | - | - | - |
| collector_heartbeat_enabled | Boolean | ON | ハートビート コレクターを有効にします |
| collector_network_activity_enabled | Boolean | ON | ネットワーク アクティビティ コレクターを有効にします |
| collector_system_information_enabled | Boolean | ON | システム情報コレクターを有効にします |

## <a name="supported-security-alerts-and-recommendations"></a>サポートされているセキュリティ アラートと推奨事項

Azure RTOS 用のセキュリティ モジュールでは、特定のセキュリティ アラートと推奨事項がサポートされています。 サービスに対する[関連するアラートと推奨事項の値を確認してカスタマイズ](concept-rtos-security-alerts-recommendations.md)します。

## <a name="log-analytics-optional"></a>Log Analytics (省略可能)

省略可能で必須ではありませんが、デバイスのイベントとアクティビティをさらに詳しく調査したい場合、Log Analytics を有効にして構成すると役立つ場合があります。 [Defender for IoT サービスと Log Analytics](how-to-security-data-access.md#log-analytics) を設定して使用する方法の詳細を参照してください。 

## <a name="next-steps"></a>次の手順

- Azure RTOS 用のセキュリティ モジュールの[セキュリティ アラートと推奨事項](concept-rtos-security-alerts-recommendations.md)を確認してカスタマイズします
- 必要に応じて [Azure RTOS 用のセキュリティ モジュール API](azure-rtos-security-module-api.md) に関する記事を参照します。

