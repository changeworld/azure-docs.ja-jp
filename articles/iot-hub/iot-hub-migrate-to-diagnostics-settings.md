---
title: Azure IoT Hub を診断設定に移行する | Microsoft Docs
description: IoT ハブに対する操作の状態をリアルタイムで監視するために、操作の監視の代わりに Azure 診断の設定を使用するように Azure IoT Hub を更新する方法。
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 3cb0f91f3143e6a4828548f3a15678b3814cba17
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154863"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>IoT Hub を操作の監視から診断設定に移行する

[操作の監視](iot-hub-operations-monitoring.md)を使用して IoT Hub での操作の状態を追跡するユーザーは、そのワークフローを、Azure Monitor の機能である [Azure 診断設定](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)に移行することができます。 診断設定は、多数の Azure サービスについてリソース レベルの診断情報を提供します。

IoT Hub の操作の監視機能は非推奨となっており、今後廃止される予定です。 この記事では、ワークロードを操作の監視から診断設定に移動する手順について説明します。 廃止のスケジュールについて詳しくは、「[Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)」(Azure Monitor および Azure Resource Health による Azure IoT ソリューションの監視) をご覧ください。

## <a name="update-iot-hub"></a>IoT Hub の更新

Azure Portal で IoT Hub を更新するには、まず診断設定を有効にし、次に操作の監視を無効にします。  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>操作の監視を無効にする

ワークフローで新しい診断設定をテストしたら、操作の監視機能を無効にすることができます。 

1. IoT Hub メニューで **[操作の監視]** を選択します。

2. 各監視カテゴリの下にある **[なし]** を選択します。

3. 操作の監視の変更を保存します。

## <a name="update-applications-that-use-operations-monitoring"></a>操作の監視を使用するアプリケーションを更新する

操作の監視と診断設定では、スキーマがやや異なります。 操作の監視を現在使用しているアプリケーションが、診断設定で使用するスキーマにマップされるように更新することが重要です。 

また、診断設定には 5 つの新しいカテゴリ用の追跡が用意されています。 既存のスキーマについてアプリケーションを更新した後、新しいカテゴリーも追加します。

* クラウドからデバイスへのツイン操作
* デバイスからクラウドへのツイン操作
* ツイン クエリ
* ジョブ操作
* ダイレクト メソッド

具体的なスキーマ構造については、[診断設定のスキーマの理解](iot-hub-monitor-resource-health.md#understand-the-logs)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Azure IoT Hub の正常性を監視し、問題をすばやく診断する](iot-hub-monitor-resource-health.md)