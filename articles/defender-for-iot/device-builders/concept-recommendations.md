---
title: IoT Hub のセキュリティに関する推奨事項
description: セキュリティに関する推奨事項の概念と Defender for IoT Hub での使用方法について説明します。
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: c2fcc071c78838f5575c40288db37f51074c12de
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263707"
---
# <a name="security-recommendations-for-iot-hub"></a>IoT Hub のセキュリティに関する推奨事項

Defender for IoT では、Azure リソースと IoT デバイスがスキャンされ、攻撃対象領域を減らすためのセキュリティに関する推奨事項が提供されます。
セキュリティに関する推奨事項は実践的で、顧客がセキュリティに関するベスト プラクティスに準拠できるよう支援することを目的にしています。

この記事では、IoT Hub に関してトリガーできる推奨事項の一覧を示します。

## <a name="built-in-recommendations-in-iot-hub"></a>IoT Hub の組み込みの推奨事項

推奨事項のアラートは、ご利用の環境のセキュリティ体制を改善するためのアクションの分析情報と推奨事項を示します。

### <a name="high-severity"></a>重要度レベル<bpt id="p1">**</bpt>高<ept id="p1">**</ept>

| 重大度 | Name | Data Source | 説明 | RecommendationType |
|--|--|--|--|--|
| 高 | 複数のデバイスで同一の認証資格情報が使われています | IoT Hub | IoT Hub 認証資格情報が複数のデバイスで使われています。 これは、不正なデバイスが正規のデバイスになりすましていることを示している可能性があり、悪意のあるアクターによるデバイスのなりすましのリスクもあります。 | IoT_SharedCredentials |
| 高 | IoT Edge モジュールの IoT Edge モデル ツインに構成された高レベルのアクセス許可 | IoT Hub | IoT Edge モジュールは、広範な Linux 機能またはホストレベルのネットワーク アクセス (ホスト マシンとのデータの送受信) を持ち、特権モードで実行されるように構成されています。 | IoT_PrivilegedDockerOptions |

### <a name="medium-severity"></a>重要度レベル<bpt id="p1">**</bpt>中<ept id="p1">**</ept>

| 重大度 | Name | Data Source | 説明 | RecommendationType |
|--|--|--|--|--|
| Medium | ACR リポジトリで使用されないサービス プリンシパル | IoT Hub | ACR リポジトリから IoT Edge モジュールをプルするために使用される認証スキーマには、サービス プリンシパル認証が使用されません。 | IoT_ACRAuthentication |
| Medium | TLS 暗号スイートのアップグレードが必要です | IoT Hub | セキュリティで保護されていない TLS 構成が検出されました。 TLS 暗号スイートをすぐにアップグレードすることをお勧めします。 | IoT_VulnerableTLSCipherSuite |
| Medium | 既定の IP フィルター ポリシーを拒否にする必要があります | IoT Hub | 既定の IP フィルター構成では、許可されるトラフィックを定義する規則を設定し、その他のトラフィックはすべて拒否する必要があります | IoT_IPFilter_DenyAll |
| Medium | IP フィルター ルールには幅広い IP 範囲が含まれています | IoT Hub | IP フィルター規則ソースの許可される IP 範囲が大きすぎます。 過度に制限の少ない規則では、ご使用の IoT ハブが悪意のあるアクターに公開されてしまう可能性があります。 | IoT_IPFilter_PermissiveRule |
| Medium | IP フィルターに推奨される規則 | IoT Hub | IP フィルターを、IotHub の動作によって取得され次の規則に変更することをお勧めします。 | IoT_RecommendedIpRulesByBaseLine |
| Medium | SecurityGroup に不整合なモジュール設定があります | IoT Hub | このデバイス セキュリティ グループ内で、セキュリティ グループのその他の項目と比較すると、異常なデバイスは IoT Edge モジュールの設定が一貫していません。 | IoT_InconsistentModuleSettings |

### <a name="low-severity"></a>重大度: 低

| 重大度 | Name | Data Source | 説明 | RecommendationType |
|--|--|--|--|--|
| 低 | IoT Edge Hub メモリを最適化できます | IoT Hub | ソリューション内の Edge モジュールによって使用されていないプロトコルのプロトコル ヘッドをオフにして、IoT Edge Hub のメモリ使用量を最適化します。 | IoT_EdgeHubMemOptimize |
| 低 | IoT Edge モジュールにログが構成されていません | IoT Hub | この IoT Edge モジュールでは、ログが無効になっています。 | IoT_EdgeLoggingOptions |

## <a name="next-steps"></a>次のステップ

- [クラシック Defender for IoT デバイスのセキュリティ アラート](agent-based-security-alerts.md)の詳細を理解します
