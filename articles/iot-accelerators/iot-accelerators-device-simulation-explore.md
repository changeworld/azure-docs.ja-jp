---
title: デバイス シミュレーション ソリューションを使ってみる - Azure | Microsoft Docs
description: IoT アクセラレータ シミュレーション ソリューションは、IoT ソリューションの開発とテストの支援に使うことができるツールです。 シミュレーション サービスはスタンドアロンで提供され、他のソリューション アクセラレータと組み合わせて使ったり、ユーザー独自のカスタム ソリューションで使ったりすることができます。
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 742998dce07f6ceef0ad906831c60f11a7d08bd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
---
# <a name="device-simulation-walkthrough"></a>デバイス シミュレーションのチュートリアル

Azure IoT デバイス シミュレーションは、IoT ソリューションの開発とテストの支援に使うことができるツールです。 デバイス シミュレーションはスタンドアロンで提供され、他のソリューション アクセラレータやユーザー独自のカスタム ソリューションと組み合わせて使うことができます。

このチュートリアルでは、デバイス シミュレーションの一部の機能を紹介します。 動作の仕組みを説明し、独自の IoT ソリューションのテストに使うことができるようにします。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * シミュレーションを構成する
> * シミュレーションを開始および停止する
> * テレメトリ メトリックを表示する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、Azure サブスクリプションに Azure IoT デバイス シミュレーションのインスタンスが展開されている必要があります。

デバイス シミュレーションをまだ展開していない場合は、[Azure IoT デバイス シミュレーションの展開](iot-accelerators-device-simulation-deploy.md)に関するチュートリアルを先に行う必要があります。

## <a name="configuring-device-simulation"></a>デバイス シミュレーションの構成

デバイス シミュレーションは、ダッシュボード内から完全に構成して実行できます。 IoT ソリューション アクセラレータの [[プロビジョニングされたソリューション]](https://www.azureiotsuite.com/) ページからダッシュボードを開きます。 新しいデバイス シミュレーションの展開の **[起動]** をクリックします。

### <a name="target-iot-hub"></a>IoT Hub をターゲットにする

あらかじめプロビジョニングされている IoT Hub または他の IoT Hub で、デバイス シミュレーションを使うことができます。

![IoT Hub を対象にする](./media/iot-accelerators-device-simulation-explore/targethub.png)

> [!NOTE]
> あらかじめプロビジョニングされている IoT Hub を使うオプションは、デバイス シミュレーションを展開するときに新しい IoT Hub を作成した場合にのみ利用できます。 IoT Hub がない場合は、いつでも [Azure Portal](https://portal.azure.com) で新しく作成できます。

特定の IoT Hub を対象にするには、**iothubowner** 接続文字列を提供します。 この接続文字列は、[Azure Portal](https://portal.azure.com) から取得できます。

1. Azure Portal の IoT Hub 構成ページで、**[共有アクセス ポリシー]** をクリックします。
1. **iothubowner** をクリックします。
1. プライマリ キーまたはセカンダリ キーをコピーします。
1. この値を、対象にする IoT Hub の下のテキスト ボックスに貼り付けます。

![IoT Hub を対象にする](./media/iot-accelerators-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>デバイスのモデル

デバイス モデルでは、シミュレートするデバイスの種類を選ぶことができます。 事前構成済みのデバイス モデルの 1 つを選ぶか、カスタム デバイス モデル用にセンサーのリストを定義することができます。

![デバイスのモデル](./media/iot-accelerators-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>構成済みのデバイス モデル

デバイス シミュレーションでは、3 つの構成済みデバイス モデルが用意されています。 冷却装置用、エレベーター用、トラック用のデバイス モデルを利用できます。

構成済みデバイス モデルには、高度な動作が JavaScript ファイルで定義された複数のセンサーが含まれます。 このようなカスタム動作は Web UI ではサポートされていません。 

次の表では、各構成済みデバイス モデルの構成の一覧を示します。

| デバイスのモデル | センサー | 単位 | 
| -------------| ------ | -----| 
| 冷却装置 | 湿度 | % |
| | pressure | psig | 
| | 温度 | F | 
| エレベーター | 床 | 
| | 振動 | MM | 
| | 気温 | F | 
| トラック | Latitude | |
| | Longitude | | 
| | 速度 | mph | 
| | 積み荷の温度 | F | 

#### <a name="custom-device-model"></a>カスタム デバイス モデル

カスタム デバイス モデルを使うと、実際のデバイスをより正確に表すセンサーをモデル化できます。 カスタム デバイスでは、最大 10 個のカスタム センサーを使うことができます。

カスタム デバイス モデル タイプを選んだら、**[+ センサーの追加]** をクリックして新しいセンサーを追加できます。

![センサーを追加する](./media/iot-accelerators-device-simulation-explore/customsensors.png)

カスタム センサーには次のプロパティがあります。

| フィールド | [説明] |
| ----- | ----------- |
| Sensor Name (センサー名) | センサーのわかりやすい名前です (例: **温度**、**速度**)。 |
| 動作 | 動作を使うと、メッセージごとにテレメトリ データを変えて、実際のデータをシミュレートできます。 **[Increment]\(増加\)** は、最小値から始めて、送信されるメッセージごとに値を 1 ずつ増やします。 最大値に達したら、再び最小値から開始します。 **[Decrement]\(減少\)** は **[Increment]\(増加\)** と同じ方法で、最大値から値を減らします。 **[Random]\(ランダム\)** 動作は、最小値と最大値の間でランダムな値を生成します。 |
| Min Value (最小値) | 許容範囲を表す下限の値です。 |
| Max Value (最大値) | 許容範囲を表す上限の値です。 |
| 単位 | °F や MPH など、センサーの測定の単位です。 |

### <a name="number-of-devices"></a>Number of devices (デバイス数)

現在、デバイス シミュレーションでは最大 20,000 個のデバイスをシミュレートできます。

![Number of devices (デバイス数)](./media/iot-accelerators-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Telemetry frequency (テレメトリ頻度)

テレメトリ頻度では、シミュレートされたデバイスが IoT hub にデータを送信する周期を指定できます。 10 秒ごとの高頻度から、99 時間 59 分 59 秒ごとの低頻度までの範囲で、データを送信できます。

![Telemetry frequency (テレメトリ頻度)](./media/iot-accelerators-device-simulation-explore/frequency.png)

> [!NOTE]
> あらかじめプロビジョニングされた IoT Hub 以外の IoT Hub を使っている場合は、対象の IoT Hub のメッセージ制限を考慮する必要があります。 たとえば、S1 ハブに 10 秒ごとにテレメトリを送信する 1,000 台のデバイスをシミュレートした場合、わずか 1 時間強でハブのテレメトリ制限に達します。

### <a name="simulation-duration"></a>シミュレーション期間

特定の時間だけシミュレーションを実行することも、明示的に停止するまで実行を続けることもできます。 特定の期間を選ぶ場合は、10 分から最大 99 時間 59 分 59 秒までの任意の期間を指定できます。

![シミュレーション期間](./media/iot-accelerators-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>シミュレーションの開始と停止

すべての必要な構成データをフォームに追加すると、**[シミュレーションの開始]** ボタンが有効になります。 シミュレーションを開始するには、このボタンをクリックします。

![シミュレーションの開始](./media/iot-accelerators-device-simulation-explore/start.png)

シミュレーションに特定の期間を指定した場合、時間が経過するとシミュレーションは自動的に停止します。 いつでも、**[シミュレーションの停止]** をクリックしてシミュレーションを早く停止できます。

シミュレーションの無期限実行を選んだ場合は、**[シミュレーションの停止]** をクリックするまで実行し続けます。 ブラウザーを閉じてデバイス シミュレーションのページに戻り、いつでもシミュレーションを停止できます。

![シミュレーションの停止](./media/iot-accelerators-device-simulation-explore/stop.png)

> [!NOTE]
> 実行できるシミュレーションは一度に 1 つだけです。 新しいシミュレーションを開始する前に、現在実行中のシミュレーションを停止する必要があります。
