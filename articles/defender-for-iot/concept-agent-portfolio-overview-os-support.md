---
title: エージェント ポートフォリオの概要と OS サポート (プレビュー)
description: Azure Defender for IoT では、デバイスの種類に基づいて、エージェントの大規模なポートフォリオが提供されます。
ms.date: 05/02/2021
ms.topic: conceptual
ms.openlocfilehash: 5449f0ac3f3c2e37a79ba156f11042b640786959
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108735013"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>エージェント ポートフォリオの概要と OS サポート (プレビュー)

Azure Defender for IoT では、デバイスの種類に基づいて、エージェントの大規模なポートフォリオが提供されます。 

## <a name="standalone-agent"></a>スタンドアロンのエージェント

スタンドアロンのエージェントは、ほとんどの Linux オペレーティング システム (OS) に対応しています。ファームウェアの一部として組み込むことができるバイナリ パッケージまたはソース コードとしてデプロイすることができ、お客様のニーズに基づいて変更やカスタマイズができます。 サポートされる OS の例を次に示します。 

| オペレーティング システム | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

詳細な情報、サポートされるオペレーティング システム、またはデバイスのファームウェアの一部としてソース コードを組み込むためのソース コードへのアクセスの要求については、アカウント マネージャーに問い合わせるか、<defender_micro_agent@microsoft.com> に電子メールを送信してください。 

## <a name="azure-rtos-micro-agent"></a>Azure RTOS マイクロ エージェント

Azure Defender for IoT マイクロ エージェントでは、Azure RTOS を使用するデバイス向けの包括的で軽量のセキュリティ ソリューションが提供されます。 また、リアルタイム オペレーティング システム (RTOS) デバイスに関する一般的な脅威と潜在的な悪意のあるアクティビティへの対応が提供されます。 マイクロ エージェントは、Azure RTOS NetX Duo コンポーネントの一部として組み込まれており、デバイスのネットワーク アクティビティを監視します。 

Azure Defender for IoT マイクロ エージェントは、Azure RTOS NetX Duo コンポーネントの一部として組み込まれており、デバイスのネットワーク アクティビティを監視します。 マイクロ エージェントは、リアルタイム オペレーティング システム (RTOS) デバイスに関する一般的な脅威と潜在的な悪意のあるアクティビティへの対応を提供する、包括的で軽量のセキュリティ ソリューションで構成されています。

## <a name="next-steps"></a>次の手順

[スタンドアロンのマイクロ エージェントの概要 (プレビュー)](concept-standalone-micro-agent-overview.md) についてさらに学習します。
