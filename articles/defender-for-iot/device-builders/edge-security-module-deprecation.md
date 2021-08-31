---
title: 機能のサポートと廃止
description: Defender for IoT では、2022 年 3 月 1 日まで C、C#、Edge のサポートが継続されます。
ms.date: 07/18/2021
ms.topic: how-to
ms.openlocfilehash: 37848fea276b7925e4e8bd711dd1abe4c0bd338e
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2021
ms.locfileid: "114400888"
---
# <a name="feature-support-and-retirement"></a>機能のサポートと廃止

この記事では、Azure Defender for IoT の機能と、Defender for IoT 内のさまざまな機能のサポートについて説明します。

## <a name="defender-for-iot-c-c-and-edge-defender-iot-micro-agent-deprecation"></a>Defender for IoT C、C#、および Edge の Defender-IoT-micro-agent の非推奨

現在の C、C#、および Edge の Defender-IoT-micro-agent は、新しいマイクロ エージェントによって置き換わります。  

新しいマイクロ エージェント開発は、知識、およびクラシック セキュリティ モジュールの開発、顧客、パートナーからのフィードバックによって収集された経験に基づいており、次の 4 つの重要な改善が行われています。

- **深度セキュリティの値**: 新しいエージェントはホスト レベルで実行されるため、デバイスの基礎となる操作が見やすくなり、セキュリティの網羅性が向上します。

- **デバイスのパフォーマンスの改善と占有領域の縮小**: 小さな RAM と ROM メモリの占有領域、および低い CPU 使用率によって実現しています。  

- **プラグ アンド プレイ**: 新しいマイクロ エージェントにはカーネル レベルの依存関係がなくなり、ソフトウェアの依存関係はすべてパッケージの一部として提供されます。 マイクロ エージェントでは、一般的な CPU アーキテクチャがサポートされています。

- **展開の容易さ**: マイクロ エージェントでは、ソース コードを使用して、バイナリ パッケージとしてさまざまな分散モデルがサポートされています。 

### <a name="timeline"></a>タイムライン 

Defender for IoT では、2022 年 3 月 1 日まで C、C#、Edge のサポートが継続されます。 

## <a name="micro-agent-preview-support"></a>マイクロ エージェントのプレビュー サポート

プレビュー期間中は、マイクロ エージェントで予告なしに重大な変更が発生する場合があります。

## <a name="next-steps"></a>次のステップ

「[Azure Defender for IoT エージェントについてよく寄せられる質問](resources-agent-frequently-asked-questions.md)」をご覧ください。