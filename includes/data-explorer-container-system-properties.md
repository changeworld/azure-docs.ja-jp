---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779954"
---
### <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

上の表の「**データ ソース**」セクションで **[イベント システムのプロパティ]** を選択した場合は、[Web UI](https://dataexplorer.azure.com/) に移動して、マッピングを適切に作成するための該当する KQL コマンドを実行します。

   **CSV マッピングの場合:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **JSON マッピングの場合:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * マッピングには、選択したプロパティをすべて含める必要があります。 
   > * CSV マッピングではプロパティの順序が重要となります。 システム プロパティは、他のどのプロパティよりも前に指定してください。また、 **[イベント システムのプロパティ]** ドロップダウンでの表示順と同じ順序で指定する必要があります。