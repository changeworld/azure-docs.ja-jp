---
title: Azure Stream Analytics ジョブの互換性レベルを理解する
description: Azure Stream Analytics ジョブの互換性レベルを設定する方法と、最新の互換性レベルでの大きな変更について説明します
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: b0e0f26abbf8eb5cbf1cf9ba2014204d773ae15d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187315"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブの互換性レベル
 
互換性レベルとは、Azure Stream Analytics サービスのリリース固有の動作のことです。 Azure Stream Analytics は管理されたサービスであり、定期的に機能が更新され、パフォーマンスが向上しています。 通常、更新は自動的にエンド ユーザーが使用できるようになります。 ただし、一部の新機能では、大きな変更が行われている場合があります。たとえば、既存のジョブの動作の変更、これらのジョブのデータを使用する処理の変更などです。互換性レベルは、Stream Analytics に導入された大きな変更を表すために使用されます。 大きな変更は、常に新しい互換性レベルで導入されます。 

互換性レベルによって、既存のジョブをエラーなしで実行できることが確認できます。 新しい Stream Analytics ジョブを作成する場合は、利用できる最新の互換性レベルを使用して作成することをお勧めします。 
 
## <a name="set-a-compatibility-level"></a>互換性レベルの設定 

互換性レベルは、ストリーム分析ジョブの実行時の動作を制御します。 Stream Analytics ジョブの互換性レベルを設定するには、ポータルを使用するか、[create job REST API 呼び出し](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)を使用します。 Azure Stream Analytics では、現在、"1.0" と "1.1" の 2 つの互換性レベルがサポートされています。 既定では、互換性レベルは "1.0" に設定されています。このレベルは、Azure Stream Analytics の一般公開時に導入されました。 既定値を更新するには、既存の Stream Analytics ジョブに移動し、**[構成]** セクションの **[互換性レベル]** オプションを選択して、値を変更します。 

互換性レベルを更新する前に、ジョブを停止してください。 ジョブが実行状態の場合は、互換性レベルを更新できません。 

![Azure portal の Stream Analytics の互換性レベル](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
互換性レベルを更新すると、T-SQL コンパイラが、選択された互換性レベルに対応する構文を使用してジョブを検証します。 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>最新の互換性レベル (1.1) での大きな変更

互換性レベル 1.1 では、以下の大きな変更が導入されました。

* **Service Bus XML 形式**  

  * **以前のバージョン:** Azure Stream Analytics が DataContractSerializer を使用していたため、メッセージのコンテンツに XML タグが含まれていました。 例: 
    
    @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001 

  * **現在のバージョン:** メッセージのコンテンツにはストリームが直接含まれていて、追加のタグは含まれていません。 例: 
  
    { "SensorId":"1", "Temperature":64} 
 
* **フィールド名の大文字と小文字の区別の保持**  

  * **以前のバージョン:** Azure Stream Analytics エンジンによる処理で、フィールド名が小文字に変更されました。 

  * **現在のバージョン:** Azure Stream Analytics エンジンによる処理で、フィールド名の大文字と小文字の区別が保持されます。 

    > [!NOTE] 
    > 大文字と小文字の区別の保持は、Edge 環境を使用してホストされている Stream Analytic ジョブではまだ利用できません。 その結果、ジョブが Edge にホストされている場合は、すべてのフィールド名が小文字に変換されます。 

* **FloatNaNDeserializationDisabled**  

  * **以前のバージョン:** CREATE TABLE コマンドが FLOAT 列型で NaN (Not-a-Number。 たとえば、Infinity、-Infinity) のイベントをフィルター処理しませんでした。これらの数値の文書化されている範囲を超えているためです。

  * **現在のバージョン:** CREATE TABLE で、強力なスキーマを指定することができます。 Stream Analytics エンジンによって、データがこのスキーマに準拠していることが検証されます。 このモデルでは、NaN 値を持つイベントをコマンドがフィルター処理できます。 

* **JSON の日時文字列の自動アップキャストの無効化**  

  * **以前のバージョン:** JSON パーサーが、日付/時刻/ゾーン情報を持つ文字列値を DateTime 型に自動的にアップ キャストし、UTC に変換しました。 そのため、タイムゾーン情報が失われていました。

  * **現在のバージョン:** 日付/時刻/ゾーン情報を持つ文字列値を DateTime 型に自動的にアップキャストすることはなくなりました。 そのため、タイムゾーン情報は保持されます。 

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の入力をトラブルシューティングする](stream-analytics-troubleshoot-input.md)
* [Stream Analytics の [リソース正常性] ブレード](stream-analytics-resource-health.md)
