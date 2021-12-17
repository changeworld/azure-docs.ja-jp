---
title: エージェント ポートフォリオの概要と OS サポート (プレビュー)
description: Microsoft Defender for IoT では、デバイスの種類に基づいて、大規模なエージェント ポートフォリオが提供されます。
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: fbad1c486a9bf553b7f7effffe8dcf5e12b5d03a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319018"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>エージェント ポートフォリオの概要と OS サポート (プレビュー)

Microsoft Defender for IoT では、デバイスの種類に基づいて、大規模なエージェント ポートフォリオが提供されます。

## <a name="standalone-agent"></a>スタンドアロンのエージェント

スタンドアロンのエージェントは、ほとんどの Linux オペレーティング システム (OS) に対応しています。ファームウェアの一部として組み込むことができるバイナリ パッケージまたはソース コードとしてデプロイすることができ、お客様のニーズに基づいて変更やカスタマイズができます。 サポートされる OS の例を次に示します。

| オペレーティング システム | AMD64 | ARM32v7 | ARM64 |
|--|--|--|--|
| Debian 9 | ✓ | ✓ | |
| Ubuntu 18.04 | ✓ |  | ✓ |
| Ubuntu 20.04 | ✓ |  | |

マイクロ エージェントでのオペレーティング システムの依存関係に関する詳細については、「[Linux の依存関係](concept-micro-agent-linux-dependencies.md#linux-dependencies)」を参照してください。

追加情報、サポートされるオペレーティング システム、またはデバイスのファームウェアの一部としてソース コードを組み込むためのソース コードへのアクセスの要求については、アカウント マネージャーに問い合わせるか、<defender_micro_agent@microsoft.com> に電子メールを送信してください。

## <a name="azure-rtos-micro-agent"></a>Azure RTOS マイクロ エージェント

Microsoft Defender for IoT マイクロ エージェントでは、Azure RTOS を使用するデバイス向けの包括的で軽量なセキュリティ ソリューションが提供されます。 Microsoft Defender for IoT マイクロ エージェントではまた、リアルタイム オペレーティング システム (RTOS) デバイスにおける一般的な脅威と悪意が潜在する行為に対応します。 マイクロ エージェントは、Azure RTOS NetX Duo コンポーネントの一部として組み込まれており、デバイスのネットワーク アクティビティを監視します。

Microsoft Defender for IoT マイクロ エージェントは、Azure RTOS NetX Duo コンポーネントの一部として組み込まれており、デバイスのネットワーク アクティビティを監視します。 マイクロ エージェントは、リアルタイム オペレーティング システム (RTOS) デバイスに関する一般的な脅威と潜在的な悪意のあるアクティビティへの対応を提供する、包括的で軽量のセキュリティ ソリューションで構成されています。

## <a name="next-steps"></a>次の手順

[マイクロ エージェントでの Linux との依存関係 (プレビュー)](concept-micro-agent-linux-dependencies.md) についてさらに学習します。

[スタンドアロンのマイクロ エージェントの概要 (プレビュー)](concept-standalone-micro-agent-overview.md) についてさらに学習します。
