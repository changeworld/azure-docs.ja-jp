---
title: Azure Stream Analytics で間違った形式の入力イベントのトラブルシューティングを行う | Microsoft Docs
description: Stream Analytics ジョブで入力データのどのイベントが問題を引き起こしているかを知る方法
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Stream Analytics での一般的な問題とトラブルシューティングの手順

## <a name="troubleshoot-for-malformed-input-events"></a>間違った形式の入力イベントのトラブルシューティング

Stream Analytics ジョブの入力ストリームに間違った形式のメッセージが含まれている場合は、シリアル化の問題が発生します。 間違った形式のメッセージには、JSON オブジェクトのかっこの欠落や不適切なタイム スタンプ形式などの不適切なシリアル化が含まれます。 Stream Analytics ジョブは、間違った形式のメッセージを受信すると、メッセージを削除し、ユーザーに警告で通知します。 警告の記号は、Stream Analytics ジョブの **[入力]** タイルに表示されます (この警告記号は、ジョブが実行中の状態である間、表示され続けます)。

![[入力] タイル](media/stream-analytics-malformed-events/inputs_tile.png)

診断ログを有効にすると、警告の詳細を参照することができます。 不正な形式の入力イベントがある場合は、実行ログに次のようなメッセージのエントリが記録されます: "メッセージ: リソース <blob URI> からの入力イベントを json とし逆シリアル化できませんでした)"。 

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

1. [入力] タイルに移動し、クリックして警告を表示します。
2. [入力の詳細] タイルに、一連の警告が問題に関する詳細と共に表示されます。 警告メッセージの例を次に示します、警告メッセージは、間違った形式の JSON データが存在するパーティション、オフセット、シーケンス番号を示します。 

   ![オフセットを含む警告メッセージ](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 不正な形式の JSON データを取得するには、CheckMalformedEvents.cs コードを実行します。これは [GitHub サンプル リポジトリ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)から取得できます。 このコードは、パーティション ID とオフセットを読み取り、そのオフセットにあるデータを出力します。 

4. データの読み取り後、シリアル化形式を分析し、修正できます。 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Azure SQL Database を Stream Analytics ジョブの出力として使用している場合の重複レコードの処理

Azure SQL Database を Stream Analytics ジョブへの出力として構成すると、宛先テーブルにレコードが一括挿入されます。 通常、Azure Stream Analytics では、出力シンクに対して[最低 1 回の配信]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)が保証されますが、SQL テーブルで一意制約が定義されている場合は、[厳密に 1 回だけの配信を保証]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)することもできます。 

SQL テーブルで一意キー制約がセットアップされた場合、SQL テーブルに重複レコードが挿入されると、Azure Stream Analytics はデータを複数のバッチに分割し、重複レコードが 1 つになるまでそれらのバッチを再帰的に挿入することで、重複レコードを削除します。 パイプライン内にかなりの数の重複行がある場合、データを分割して再帰的に挿入し、重複を 1 つずつ無視していくことは、時間のかかるプロセスになります。 過去 1 時間以内のアクティビティ ログにキー違反の警告メッセージが複数ある場合は、SQL 出力によってジョブ全体の速度が低下している可能性があります。 この問題を解決するには、IGNORE_DUP_KEY オプションを有効にして、キー違反の原因となっている[インデックスを構成する]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)必要があります。 このオプションを有効にすると、一括挿入時の重複値は SQL によって無視され、SQL Azure ではエラーの代わりに警告メッセージが生成されます。 Azure Stream Analytics では、主キー違反エラーが生成されなくなります。

IGNORE_DUP_KEY を構成する際には、一部のインデックスに関する次の考慮事項に注意してください。

* ALTER INDEX を使用する主キーや一意制約については、IGNORE_DUP_KEY を設定することはできません。インデックスをドロップし、再作成する必要があります。  
* 一意なインデックスについては、IGNORE_DUP_KEY オプションを設定できます。このインデックスは、主キー/一意制約とは異なり、CREATE INDEX または INDEX 定義を使用して作成されます。  
* IGNORE_DUP_KEY は、列ストア インデックスには適用されません (このようなインデックスに対して一意性を強制することはできないため)。  

## <a name="next-steps"></a>次の手順

* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

