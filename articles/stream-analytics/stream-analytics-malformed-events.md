---
title: "Azure Stream Analytics で間違った形式の入力イベントのトラブルシューティングを行う | Microsoft Docs"
description: "Stream Analytics ジョブで入力データのどのイベントが問題を引き起こしているかを知る方法"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2018
---
# <a name="troubleshoot-for-malformed-input-events"></a>間違った形式の入力イベントのトラブルシューティング

Stream Analytics ジョブの入力ストリームに間違った形式のメッセージが含まれている場合は、シリアル化の問題が発生します。 間違った形式のメッセージには、JSON オブジェクトのかっこの欠落や不適切なタイム スタンプ形式などの不適切なシリアル化が含まれます。 Stream Analytics ジョブは、間違った形式のメッセージを受信すると、メッセージを削除し、ユーザーに警告で通知します。 警告記号は、Stream Analytics ジョブの **[入力]** タイルの上に表示されます。

![[入力] タイル](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

1. [入力] タイルに移動し、クリックして警告を表示します。
2. [入力の詳細] タイルに、一連の警告が問題に関する詳細と共に表示されます。 警告メッセージの例を次に示します、警告メッセージは、間違った形式の JSON データが存在するパーティション、オフセット、シーケンス番号を示します。 

   ![オフセットを含む警告メッセージ](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 不適切な形式の JSON データを取得するには、次のコード スニペットを実行します。 このコード ブロックは、パーティション ID、オフセットを読み取り、データを出力します。 [GitHub サンプル リポジトリ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)から完全なサンプルを取得することができます。 データの読み取り後、シリアル化形式を分析し、修正できます。

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>次の手順

* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
