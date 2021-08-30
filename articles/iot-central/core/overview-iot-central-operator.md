---
title: Azure IoT Central オペレーター ガイド
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、IoT Central のオペレーター ロールの概要を示します。
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 86a5d327e00ee7c9595cc586aa7e63023a386bba
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586547"
---
# <a name="iot-central-operator-guide"></a>IoT Central オペレーター ガイド

IoT Central アプリケーションを使用すると、数百万台ものデバイスを、そのライフ サイクル全体にわたって監視および管理することができます。 このガイドは、IoT Central アプリケーションを使用して IoT デバイスを管理するオペレーターを対象としています。

オペレーター:

- アプリケーションに接続されるデバイスを監視および管理します。
- デバイスの問題をトラブルシューティングおよび修復します。
- 新しいデバイスをプロビジョニングします。

## <a name="monitor-and-manage-devices"></a>デバイスの監視と管理

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="デバイス ビューを示すスクリーンショット":::

デバイスを監視するために、オペレーターは、ソリューション作成者によってデバイス テンプレートの一部として定義されたデバイス ビューを使用できます。 これらのビューでは、デバイスのテレメトリとプロパティの値を表示できます。 前のスクリーンショットで示した **[概要]** ビューはその例です。

オペレーターは、デバイス グループと組み込みの分析機能を使用して、さらに詳細な情報を得ることができます。 詳細については、「[分析を使用してデバイス データを分析する方法](howto-create-analytics.md)」を参照してください。

個々のデバイスを管理するために、オペレーターはデバイス ビューを使用して、デバイスとクラウドのプロパティを設定したり、デバイス コマンドを呼び出したりできます。 前のスクリーンショットの **[デバイスの管理]** および **[コマンド]** ビューがその例です。

デバイスを一括管理するために、オペレーターはジョブを作成したりスケジュールしたりできます。 ジョブでは、複数のデバイスを対象に、プロパティを更新したりコマンドを実行したりできます。 詳細については、「[Azure IoT Central アプリケーションでのジョブの作成と実行](howto-manage-devices-in-bulk.md)」を参照してください。

## <a name="troubleshoot-and-remediate-issues"></a>問題のトラブルシューティングと修復

オペレーターは、アプリケーションとそのデバイスの正常性に責任を負います。 [トラブルシューティング ガイド](troubleshoot-connection.md)は、オペレーターが一般的な問題を診断および修復するために役立ちます。 オペレーターは **[デバイス]** ページを使用して、誤動作していると思われるデバイスを、問題が解決されるまでブロックすることができます。

## <a name="add-and-remove-devices"></a>デバイスの追加と削除

オペレーターは、デバイスを個別または一括で IoT Central アプリケーションに追加したり、アプリケーションから削除したりできます。 詳細については、「[Azure IoT Central アプリケーションでデバイスを管理する](howto-manage-devices-individually.md)」を参照してください。

## <a name="personalize"></a>Personalize

オペレーターは、最も頻繁に使用するリソースへのリンクを含む、個人的なダッシュボードを IoT Central アプリケーションに作成できます。 詳細については、「[ダッシュボードの管理](howto-manage-dashboards.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

IoT Central の詳細な使用方法については、次の手順として、クイックスタートの「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」から始めることをお勧めします。
