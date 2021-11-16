---
title: デバイス ビルダー向け Azure Defender for IoT の新機能
description: 最新リリースと、デバイス ビルダー向け Defender for IoT の最新機能について説明します。
ms.topic: overview
ms.date: 11/02/2021
ms.openlocfilehash: 4d6d931082f55a08667da20283d28c655a9fb584
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438590"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>デバイス ビルダー向け Azure Defender for IoT の新機能とは何か。  

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

この記事では、Defender for IoT の新機能と機能強化の一覧を示します。

記載されている機能はプレビュー段階です。 [Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはその他のまだ一般提供されていない Azure 機能に適用される他の法律条項が含まれています。

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Azure Defender for IoT のバージョン管理とサポート

以下の一覧は、Defender for IoT のサポートと破壊的変更のポリシー、および現在使用可能な Azure Defender for IoT のバージョンを示しています。

## <a name="september-2021"></a>2021 年 9 月

**バージョン 3.11**:

- **[ログイン コレクター](concept-event-aggregation.md#login-collector-event-based-collector)** - ログイン コレクターにより、ユーザーのログイン、ログアウト、失敗したログイン試行が収集されます。 SSH や Telnet など。

- **[システム情報コレクター](concept-event-aggregation.md#system-information-trigger-based-collector)** - システム情報コレクターでは、デバイスのオペレーティング システムとハードウェアの詳細に関連する情報が収集されます。

- **[イベント集計](concept-event-aggregation.md#how-does-event-aggregation-work)** - Defender for IoT エージェントによって、デバイスのセキュリティを維持しながら、送信されるメッセージの数とコストを削減するプロセス、ログイン、ネットワーク イベントなどのイベントが集計されます。  

- **[ツイン構成](concept-micro-agent-configuration.md)** - マイクロ エージェントの動作は、一連のモジュール ツイン プロパティによって構成されます。 (イベント送信頻度や集計モードなど)。 ニーズに合わせて最適になるように、マイクロ エージェントを構成できます。

## <a name="march-2021"></a>2021 年 3 月

### <a name="device-builder---new-micro-agent-public-preview"></a>デバイス ビルダー - 新しいマイクロ エージェント (パブリック プレビュー)

新しいデバイス ビルダー モジュールが登場しました。 このモジュール (マイクロ エージェントと呼ばれます) では、次のことが可能です。

- **Azure IoT Hub と Azure Defender for IoT との統合** - Azure IoT Hub と Azure Defender for IoT の両方で提供される監視オプションと統合することで、より強力なエンドポイント セキュリティを IoT デバイスに直接構築します。

- **標準的な IoT オペレーティング システムをサポートする柔軟なデプロイ オプション** - Linux や Azure RTOS などの標準的な IoT オペレーティング システムをサポートし、バイナリ パッケージまたは変更可能なソース コードとしてデプロイできます。

- **OS カーネルに依存しない最小限のリソース要件** - 占有領域が小さく、CPU 使用率が低く、OS カーネルに依存しません。

- **セキュリティ態勢管理** - IoT デバイスのセキュリティ態勢を事前に監視します。

- **継続的なリアルタイムの IoT/OT 脅威検出** - ボットネット、ブルート フォース攻撃、クリプト マイナー、疑わしいネットワーク アクティビティなどの脅威を検出します

非推奨の Defender for IoT マイクロ エージェントのドキュメントは、*デバイス ビルダー向けのエージェントベースのソリューション > Classic* フォルダーに移動されます。

この機能セットは、現在のパブリック プレビュー クラウド リリースで使用できます。

## <a name="next-steps"></a>次のステップ

[デバイス ビルダー向けのエージェントベースのソリューションとは](architecture-agent-based.md)
