---
title: エージェント ベースの推奨事項
description: セキュリティに関する推奨事項の概念と Defender for IoT デバイスでの使用方法について説明します。
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 4e0266d06f5be7937c056430aadd2d49f2f6a86d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343764"
---
# <a name="security-recommendations-for-iot-devices"></a>IoT デバイスのセキュリティに関する推奨事項

Defender for IoT では、Azure リソースと IoT デバイスがスキャンされ、攻撃対象領域を減らすためのセキュリティに関する推奨事項が提供されます。
セキュリティに関する推奨事項は実践的で、顧客がセキュリティに関するベスト プラクティスに準拠できるよう支援することを目的にしています。

この記事では、IoT デバイスに関してトリガーできる推奨事項の一覧を示します。

## <a name="agent-based-recommendations"></a>エージェント ベースの推奨事項

デバイスに関する推奨事項は、デバイスのセキュリティ体制を改善するための洞察および提案を提供します。

| 重大度 | Name | Data Source | 説明 |
|--|--|--|--|
| Medium | デバイスで開いているポート | クラシック Defender-IoT-micro-agent| リッスン中のエンドポイントがデバイス上に見つかりました。 |
| Medium | チェーンのいずれかで制限の少なすぎるファイアウォール ポリシーが見つかりました。 | クラシック Defender-IoT-micro-agent| 許可のファイアウォール ポリシーが見つかりました (入力/出力)。 ファイアウォール ポリシーは既定ではすべてのトラフィックを拒否しなければならず、デバイスとの間の必要な通信を許可するにはルールを定義する必要があります。 |
| Medium | 入力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | クラシック Defender-IoT-micro-agent| ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました。 |
| Medium | 出力チェーンで制限の少なすぎるファイアウォール ルールが見つかりました | クラシック Defender-IoT-micro-agent| ファイアウォール内に、IP アドレスまたはポートの範囲が広くて制限の少ないパターンを含むルールが見つかりました。 |
| Medium | オペレーティング システムのベースライン検証に失敗しました | クラシック Defender-IoT-micro-agent| デバイスが [CIS Linux ベンチマーク](https://www.cisecurity.org/cis-benchmarks/)に準拠していません。 |

### <a name="agent-based-operational-recommendations"></a>エージェント ベースの運用に関する推奨事項

運用に関する推奨事項は、セキュリティ エージェントの構成を改善するための洞察および提案を提供します。

| 重大度 | Name | Data Source | 説明 |
|--|--|--|--|
| 低 | エージェントが未使用のメッセージを送信します | クラシック Defender-IoT-micro-agent | 過去 24 時間の 10% 以上のセキュリティ メッセージが 4 KB 未満でした。 |
| 低 | セキュリティ ツインの構成が最適ではありません | クラシック Defender-IoT-micro-agent | セキュリティ ツインの構成が最適ではありません。 |
| 低 | セキュリティ ツインの構成に競合があります | クラシック Defender-IoT-micro-agent | セキュリティ ツインの構成で競合が検出されました。 |

## <a name="next-steps"></a>次のステップ

- Defender for IoT サービスの<bpt id="p1">[</bpt>概要<ept id="p1">](overview.md)</ept>
- <bpt id="p1">[</bpt>セキュリティ データにアクセスする<ept id="p1">](how-to-security-data-access.md)</ept>方法を学習ぶ
- <bpt id="p1">[</bpt>デバイスの調査<ept id="p1">](how-to-investigate-device.md)</ept>について学ぶ
