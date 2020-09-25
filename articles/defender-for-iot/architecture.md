---
title: ソリューションのアーキテクチャ
description: Azure Defender for IoT サービスの情報フローについて説明します。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d26d9e3d686ad7c34e7493dc1413b7a9e7a2f6b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932670"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender for IoT のアーキテクチャ

この記事では、Defender for IoT ソリューションの機能的なシステム アーキテクチャについて説明します。

## <a name="defender-for-iot-components"></a>Defender for IoT のコンポーネント

Defender for IoT は、次のコンポーネントで構成されています。

- IoT Hub 統合
- デバイス エージェント (オプション)
- セキュリティ メッセージの送信 SDK
- 分析パイプライン

### <a name="defender-for-iot-workflows"></a>Defender for IoT のワークフロー

Defender for IoT は、2 つの機能ワークフローのどちらかで動作します。組み込みと拡張です。

### <a name="built-in"></a>組み込み

**組み込み**モードでは、IoT Hub で **[セキュリティ]** オプションをオンにすると、Defender for IoT が有効になります。 リアルタイム監視、推奨事項の提示、アラート機能を備えた組み込みモードでは、デバイスの可視性と比類ないセキュリティが手軽に得られます。 組み込みモードでは、デバイスにエージェントをインストールする必要がありません。ログに記録されたアクティビティに対する高度な分析を使用してフィールド デバイスが分析され、保護されます。

### <a name="enhanced"></a>拡張

**拡張**モードでは、IoT Hub で **[セキュリティ]** オプションをオンにし、デバイスに Defender for IoT デバイス エージェントをインストールすると、エージェントによってそのデバイスから生のセキュリティ イベントが収集、集計、分析されます。 未加工のセキュリティ イベントには、IP 接続、プロセス作成、ユーザー ログイン、およびその他のセキュリティ関連情報が含まれる可能性があります。 また、Defender for IoT デバイス エージェントではイベント集計も処理され、高いネットワーク スループットを回避できます。 エージェントは高度にカスタマイズ可能です。これにより、最速の SLA で重要な情報のみを送信するなど、特定のタスクを実行したり、広範なセキュリティ情報とコンテキストをより大きなセグメントに集計して、高いサービス コストを回避したりできます。

![Defender for IoT のアーキテクチャ](./media/architecture/azure-iot-security-architecture.png)

デバイス エージェント、およびその他のアプリケーションでは、**Azure セキュリティ メッセージの送信 SDK** を使用して、Azure IoT Hub にセキュリティ情報が送信されます。 IoT Hub ではこの情報が取得され、Defender for IoT サービスに転送されます。

Defender for IoT サービスが有効になると、IoT Hub により、転送されたデータに加え、そのすべての内部データが Defender for IoT での分析用に送信されます。 このデータには、デバイス クラウド操作ログ、デバイス ID、および Hub 構成が含まれます。 この情報はすべて、Defender for IoT 分析パイプラインを作成するのに役立ちます。

また、Defender for IoT 分析パイプラインにより、Microsoft および Microsoft パートナー内のさまざまなソースから、その他の脅威インテリジェンス ストリームが受信されます。 Defender for IoT の分析パイプライン全体が、サービスに対して行われたすべての顧客構成 (カスタム アラートや、セキュリティ メッセージの送信 SDK の使用など) と連携します。

Defender for IoT により、分析パイプラインを使用して、実用的な推奨事項およびアラートを生成するためにすべての情報ストリームが結合されます。 パイプラインには、セキュリティ研究者と専門家によって作成されたカスタム ルールと、標準的なデバイスの動作とリスクの分析からの偏差を検索する機械学習モデルの両方が含まれています。

Defender for IoT の推奨事項とアラート (分析パイプライン出力) は、各顧客の Log Analytics ワークスペースに書き込まれます。 ワークスペースおよび未加工のイベントにアラートと推奨事項を含めると、検出された不審なアクティビティの正確な情報を使用して詳細な調査とクエリを行うことができます。

## <a name="next-steps"></a>次のステップ

この記事では、Defender for IoT ソリューションの基本的なアーキテクチャとワークフローについて説明しました。 IoT Hub でセキュリティ ソリューションの使用を開始し、有効化するための前提条件と方法の詳細については、次の記事を参照してください。

- [サービスの前提条件](service-prerequisites.md)
- [作業の開始](getting-started.md)
- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [IoT Hub でセキュリティを有効化する](quickstart-onboard-iot-hub.md)
- [Defender for IoT に関する FAQ](resources-frequently-asked-questions.md)
- [Defender for IoT のセキュリティ アラート](concept-security-alerts.md)
