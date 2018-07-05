---
title: Azure Time Series Insights に IoT Hub イベント ソースを追加する方法 | Microsoft Docs
description: この記事では、IoT Hub に接続されたイベント ソースを Time Series Insights 環境に追加する方法を説明します
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 1cc8518e84bd9fe7a1f03a2f5d6ccdbac8fb78e3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330596"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Time Series Insights 環境に IoT Hub イベント ソースを追加する方法
この記事では、Azure Portal を使用して、IoT Hub からデータを読み取るイベント ソースを Time Series Insights 環境に追加する方法を説明します。

## <a name="prerequisites"></a>前提条件
- Time Series Insights 環境を作成します。 詳しくは、[Azure Time Series Insights 環境の作成](time-series-insights-get-started.md)に関するページをご覧ください。 
- IoT Hub を作成します。 IoT Hub の詳細は、「[Azure Portal を使用して IoT Hub を作成する](../iot-hub/iot-hub-create-through-portal.md)」をご覧ください。
- IoT Hub には、アクティブなメッセージ イベントが送信される必要があります。
- IoT Hub に Time Series Insight 環境で使用する専用コンシューマー グループを作成します。 各 Time Series Insights イベント ソースには、他のコンシューマーと共有されない専用のコンシューマー グループが設定されている必要があります。 複数のリーダーが同じコンシューマー グループのイベントを消費すると、すべてのリーダーにエラーが発生する可能性があります。 詳細については、[IoT Hub 開発者ガイド](../iot-hub/iot-hub-devguide.md)をご覧ください。

### <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub へのコンシューマー グループの追加
コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 IoT Hub から確実にデータを読み取るため、この Time Series Insights 環境でのみ使用する専用のコンシューマー グループを提供します。

新しいコンシューマー グループを IoT Hub に追加するには、次の手順に従います。
1. Azure Portal で、IoT Hub を見つけて開きます。

2. **[メッセージング]** 見出しの下にある **[エンドポイント]** を選択します。 

   ![コンシューマー グループの追加](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. **[イベント]** エンドポイントを選択します。すると、**[プロパティ]** ページが開きます。

4. **[コンシューマー グループ]** 見出しの下で、コンシューマー グループの新しい一意の名前を指定します。 Time Series Insights 環境で新しいイベント ソースを作成する場合に、これと同じ名前を使用します。

5. **[保存]** を選択して新しいコンシューマー グループを保存します。

## <a name="add-a-new-event-source"></a>新しいイベント ソースの追加
1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 既存の Time Series Insights 環境を見つけます。 Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックします。 Time Series Insights 環境を選択します。

3. **[Environment Topology] (環境トポロジ)** 見出しで、**[イベント ソース]** をクリックします。
   ![[イベント ソース] と [+ 追加]](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. **[+ 追加]** をクリックします。

5. この Time Series Insights 環境に一意の**イベント ソース名** (**event-stream** など) を指定します。

   ![フォームの最初の 3 つのパラメーターを入力します。](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. **[ソース]** に **[IoT Hub]** を選択します。

7. 適切な**インポート オプション**を選択します。 
   - サブスクリプションのいずれかに既存の IoT Hub がある場合は、**[Use IoT Hub from available subscriptions]\(使用可能なサブスクリプションの Iot Hub を使用する\)** を選択します。 これは、最も簡単なアプローチです。
   - IoT Hub がサブスクリプションの外部であるか、または高度なオプションを選択する場合は、**[IoT ハブ設定を手動で行う]** を選択します。 

8. **[Use IoT Hub from available subscriptions]\(使用可能なサブスクリプションの Iot Hub を使用する\)** オプションを選択した場合に必要な各プロパティについて次の表に説明します。

   ![サブスクリプションとイベント ハブの詳細](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | プロパティ | 説明 |
   | --- | --- |
   | サブスクリプション ID | この IoT Hub が作成されたサブスクリプションを選択します。
   | IoT Hub 名 | IoT Hub の名前を選択します。
   | IoT Hub ポリシー名 | [IoT Hub の設定] タブにある共有アクセス ポリシーを選択します。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**サービス接続**アクセス許可が "*必要*" です。
   | IoT Hub ポリシー キー | キーが事前設定されています。
   | IoT Hub コンシューマー グループ | IoT Hub からイベントを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。
   | イベントのシリアル化の形式 | JSON は、現時点で唯一使用可能なシリアル化です。 イベント メッセージは、この形式である必要があります。そうでないとデータを読み取ることができません。 |
   | タイムスタンプ プロパティ名 | この値を決定するには、IoT Hub に送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの**名前**です。 値は、大文字小文字が区別されます。 空白のままにすると、イベント ソース内の**イベント エンキュー時間**が、イベント タイムスタンプとして使用されます。 |

9. **[IoT ハブ設定を手動で行う]** オプションを選択した場合に必要な各プロパティについて次の表に説明します。

   | プロパティ | 説明 |
   | --- | --- |
   | サブスクリプション ID | この IoT Hub が作成されたサブスクリプション。
   | リソース グループ | この IoT Hub が作成されたリソース グループ名です。
   | IoT Hub 名 | IoT Hub の名前。 IoT Hub を作成した場合は、特定の名前も付けられています。
   | IoT Hub ポリシー名 | [IoT Hub の設定] タブで作成できる共有アクセス ポリシー。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**サービス接続**アクセス許可が "*必要*" です。
   | IoT Hub ポリシー キー | Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 ここにプライマリ キーまたはセカンダリ キーを入力します。
   | IoT Hub コンシューマー グループ | IoT Hub からイベントを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。
   | イベントのシリアル化の形式 | JSON は、現時点で唯一使用可能なシリアル化です。 イベント メッセージは、この形式である必要があります。そうでないとデータを読み取ることができません。 |
   | タイムスタンプ プロパティ名 | この値を決定するには、IoT Hub に送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの**名前**です。 値は、大文字小文字が区別されます。 空白のままにすると、イベント ソース内の**イベント エンキュー時間**が、イベント タイムスタンプとして使用されます。 |

10. IoT Hub に追加した、専用の TSI コンシューマー グループ名を追加します。

11. **[作成]** を選択して、新しいイベント ソースを追加します。

   ![Click Create](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   イベント ソースの作成後、Time Series Insights は自動的に環境へのデータのストリーミングを開始します。

## <a name="next-steps"></a>次の手順
- [データ アクセス ポリシーを定義](time-series-insights-data-access.md)して、データをセキュリティ保護します。
- イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。
- [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境にアクセスします。
