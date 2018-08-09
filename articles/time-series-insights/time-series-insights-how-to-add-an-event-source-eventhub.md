---
title: Azure Time Series Insights に Event Hub イベント ソースを追加する方法 | Microsoft Docs
description: この記事では、イベント ハブに接続されたイベント ソースを Time Series Insights 環境に追加する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: ce4bf1ab74e4203f0deb7b2984ffa6a66d5efd4a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627112"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Time Series Insights 環境にイベント ハブ イベント ソースを追加する方法

この記事では、Azure Portal を使用して、イベント ハブからデータを読み取るイベント ソースを Time Series Insights 環境に追加する方法を説明します。

## <a name="prerequisites"></a>前提条件
- Time Series Insights 環境を作成します。 詳しくは、[Azure Time Series Insights 環境の作成](time-series-insights-get-started.md)に関するページをご覧ください。 
- イベント ハブの作成。 Event Hubs について詳しくは、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」を参照してください。
- イベント ハブには、アクティブなメッセージ イベントが送信される必要があります。 詳しくは、「[.NET Framework を使用して Azure Event Hubs にイベントを送信する](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)」をご覧ください。
- イベント ハブに Time Series Insight 環境で使用する専用コンシューマー グループを作成します。 各 Time Series Insights イベント ソースには、他のコンシューマーと共有されない専用のコンシューマー グループが設定されている必要があります。 複数のリーダーが同じコンシューマー グループのイベントを消費すると、すべてのリーダーにエラーが発生する可能性があります。 Event Hub ごとに 20 個のコンシューマー グループという制限もある点に注意してください。 詳細については、「[Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)」をご覧ください。

### <a name="add-a-consumer-group-to-your-event-hub"></a>イベント ハブへのコンシューマー グループの追加
コンシューマー グループは、Azure Event Hubs からデータをプルするアプリケーションによって使用されます。 イベント ハブから確実にデータを読み取るため、この Time Series Insights 環境でのみ使用する専用のコンシューマー グループを提供します。

イベント ハブに新しいコンシューマー グループを追加するには、次の手順に従います。
1. Azure Portal で、イベント ハブを見つけて、開きます。

2. **[エンティティ]** 見出しで、**[コンシューマー グループ]** を選択します。

   ![イベント ハブ - コンシューマー グループの追加](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. **[+ コンシューマー グループ]** を選択し、新しいコンシューマー グループを追加します。 

4. **[コンシューマー グループ]** ページで、新しい一意の**名前**を指定します。  Time Series Insights 環境で、新しいイベント ソースを作成する場合に、この同じ名前を使用します。

5. **[作成]** を選択して、新しいコンシューマー グループを作成します。

## <a name="add-a-new-event-source"></a>新しいイベント ソースの追加
1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 既存の Time Series Insights 環境を見つけます。 Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックします。 Time Series Insights 環境を選択します。

3. **[Environment Topology] (環境トポロジ)** 見出しで、**[イベント ソース]** をクリックします。

   ![イベント ソース + 追加](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. **[+ 追加]** をクリックします。

5. この Time Series Insights 環境に一意の**イベント ソース名** (**event-stream** など) を指定します。

   ![フォームの最初の 3 つのパラメーターを入力します。](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. **[イベント ハブ]** として **[ソース]** を 選択します。

7. 適切な**インポート オプション**を選択します。 
   - サブスクリプションのいずれかに既に既存のイベント ハブがある場合は、**[使用可能なサブスクリプションのイベント ハブを使用する]** を選択します。 これは、最も簡単なアプローチです。
   - イベント ハブがサブスクリプションの外部であるか、または高度なオプションを選択する場合は、**[イベント ハブ設定を手動で行う]** を選択します。 

8. **[使用可能なサブスクリプションのイベント ハブを使用する]** オプションを選択した場合に、必要な各プロパティについて次の表に説明します。

   ![サブスクリプションとイベント ハブの詳細](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | プロパティ | 説明 |
   | --- | --- |
   | サブスクリプション ID | このイベント ハブが作成されたサブスクリプションを選択します。
   | Service Bus 名前空間 | イベント ハブが含まれている Service Bus 名前空間を選択します。
   | イベント ハブ名 | イベント ハブの名前を選択します。
   | イベント ハブ ポリシー名 | イベント ハブの [構成] タブで作成できる共有アクセス ポリシーを選択します。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**読み取り**アクセス許可の設定が "*必須*" です。
   | イベント ハブ ポリシー キー | キー値は事前に設定されている場合があります。
   | イベント ハブ コンシューマー グループ | イベント ハブからデータを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。 |
   | イベントのシリアル化の形式 | JSON は、現時点で唯一使用可能なシリアル化です。 イベント メッセージは、この形式である必要があります。そうでないとデータを読み取ることができません。 |
   | タイムスタンプ プロパティ名 | この値を決定するには、イベント ハブに送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの**名前**です。 値は、大文字小文字が区別されます。 空白のままにすると、ベント ソース内の**イベント エンキュー時間**が、イベント タイムスタンプとして使用されます。 |


9. **[イベント ハブ設定を手動で行う]** オプションを選択した場合、必要な各プロパティについて次の表に説明します。

   | プロパティ | 説明 |
   | --- | --- |
   | サブスクリプション ID | このイベント ハブが作成されたサブスクリプション。
   | リソース グループ | このイベント ハブが作成されたリソース グループ。
   | Service Bus 名前空間 | Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。
   | イベント ハブ名 | イベント ハブの名前。 イベント ハブを作成したときに、固有の名前を設定しています。
   | イベント ハブ ポリシー名 | [イベント ハブの構成] タブで作成できる共有アクセス ポリシー。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**読み取り**アクセス許可の設定が "*必須*" です。
   | イベント ハブ ポリシー キー | Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 ここにプライマリ キーまたはセカンダリ キーを入力します。
   | イベント ハブ コンシューマー グループ | イベント ハブからデータを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。
   | イベントのシリアル化の形式 | JSON は、現時点で唯一使用可能なシリアル化です。 イベント メッセージは、この形式である必要があります。そうでないとデータを読み取ることができません。 |
   | タイムスタンプ プロパティ名 | この値を決定するには、イベント ハブに送信されるメッセージ データのメッセージ形式を理解する必要があります。 この値は、イベント タイムスタンプとして使用するメッセージ データ内の特定のイベント プロパティの**名前**です。 値は、大文字小文字が区別されます。 空白のままにすると、イベント ソース内の**イベント エンキュー時間**が、イベント タイムスタンプとして使用されます。 |

10. イベント ハブに追加した、専用の TSI コンシューマー グループ名を追加します。

11. **[作成]** を選択して、新しいイベント ソースを追加します。
   
   ![Create をクリックしてください。](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   イベント ソースの作成後、Time Series Insights は自動的に環境へのデータのストリーミングを開始します。


## <a name="next-steps"></a>次の手順
- [データ アクセス ポリシーを定義](time-series-insights-data-access.md)して、データをセキュリティ保護します。
- イベント ソースに[イベントを送信](time-series-insights-send-events.md)します。
- [Time Series Insights エクスプローラー](https://insights.timeseries.azure.com)で環境にアクセスします。
