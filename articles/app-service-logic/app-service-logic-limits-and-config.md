---
title: "ロジック アプリの制限と構成 | Microsoft Docs"
description: "Logic Apps におけるサービスの制限と使用できる構成値の概要です。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 47c13d5cbaeb114da18b710c52e2c989df4c0e01
ms.openlocfilehash: 2417d4e3d8033ff8d66ace8b27f398266a7fd101


---
# <a name="logic-app-limits-and-configuration"></a>ロジック アプリの制限と構成

以下、Azure Logic Apps に関する現在の制限と構成について詳しく取り上げます。

## <a name="limits"></a>制限

### <a name="http-request-limits"></a>HTTP 要求の制限

次に示したのは、1 回の HTTP 要求/コネクタ呼び出しに関する制限です。

#### <a name="timeout"></a>タイムアウト

|名前|制限|メモ|
|----|----|----|
|要求タイムアウト|90 秒|必要に応じて[非同期パターン](app-service-logic-create-api-app.md)または[ until ループ](app-service-logic-loops-and-scopes.md)で対応できます。|

#### <a name="message-size"></a>メッセージ サイズ

|名前|制限|メモ|
|----|----|----|
|メッセージ サイズ|50 MB|一部のコネクタと API は 50 MB をサポートしていません。 |
|式評価の制限|131,072 文字|`@concat()`、`@base64()`、`string` をこれより長くすることはできません。|

#### <a name="retry-policy"></a>再試行ポリシー

|名前|制限|メモ|
|----|----|----|
|再試行|4|[再試行ポリシー パラメーター](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)で構成できます。|
|再試行の最大間隔|1 時間|[再試行ポリシー パラメーター](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)で構成できます。|
|再試行の最小間隔|20 秒|[再試行ポリシー パラメーター](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)で構成できます。|

### <a name="run-duration-and-retention"></a>実行継続時間とリテンション期間

次に示したのは、ロジック アプリの 1 回の実行に関する制限です。

|名前|制限|メモ|
|----|----|----|
|時間継続時間|90 日間||
|ストレージのリテンション期間|90 日間|実行の開始時刻が起点となります。|
|最小の繰り返し間隔|1 秒|| App Service プランを持つロジック アプリでは 15 秒
|最大の繰り返し間隔|500 日||


### <a name="looping-and-debatching-limits"></a>ループと分割処理の制限

次に示したのは、ロジック アプリの 1 回の実行に関する制限です。

|名前|制限|メモ|
|----|----|----|
|ForEach 項目数|5,000|必要に応じて [クエリ アクション](../connectors/connectors-native-query.md) を使用することで、さらに大きな配列にフィルターを適用できます。|
|Until 反復数|5,000||
|SplitOn 項目数|5,000||
|ForEach 並列処理|20|`"operationOptions": "Sequential"` を `foreach` アクションに追加することによってシーケンシャルな foreach に設定できます。|


### <a name="throughput-limits"></a>スループットの制限

次に示したのは、1 つのロジック アプリのインスタンスに関する制限です。 

|名前|制限|メモ|
|----|----|----|
|1 秒あたりのトリガー数|100|必要に応じて複数のアプリにワークフローを分散させることができます。|

### <a name="definition-limits"></a>定義の制限

次に示したのは、ロジック アプリの 1 つの定義に関する制限です。

|名前|制限|メモ|
|----|----|----|
|ワークフローごとのアクション数|250|この制限は、入れ子にしたワークフローを必要に応じて追加することで拡張できます。|
|アクションの入れ子に許容される深さ|5|この制限は、入れ子にしたワークフローを必要に応じて追加することで拡張できます。|
|サブスクリプションごとの 1 リージョンあたりのフロー数|1,000||
|ワークフローごとのトリガー数|10||
|式ごとの最大文字数|8,192||
|最大 `trackedProperties` サイズ (文字数)|16,000|
|`action`/`trigger` 名の制限|80||
|`description` の長さの制限|256||
|`parameters` の制限|50||
|`outputs` の制限|10||

### <a name="integration-account-limits"></a>統合アカウントの制限

統合アカウントに追加されるアーティファクトには、以下の制限があります。

|名前|制限|メモ|
|----|----|----|
|スキーマ|8 MB|[BLOB URI](app-service-logic-enterprise-integration-schemas.md) を使用すると、2 MB を超えるファイルをアップロードできます。 |
|マップ (XSLT ファイル)|2 MB| |

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>B2B プロトコル (AS2、X12、EDIFACT) のメッセージのサイズ

B2B プロトコルには、以下の制限があります。

|名前|制限|メモ|
|----|----|----|
|AS2|50 MB|デコードおよびエンコードに適用可能|
|X12|50 MB|デコードおよびエンコードに適用可能|
|EDIFACT|50 MB|デコードおよびエンコードに適用可能|

## <a name="configuration"></a>構成

### <a name="ip-address"></a>IP アドレス

[コネクタ](../connectors/apis-list.md) から実行される呼び出しは、以下に示した IP アドレスが発信元となります。

ロジック アプリから直接 ([HTTP](../connectors/connectors-native-http.md) または [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) を介して) 実行される呼び出しは、[Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)に該当するいずれかのアドレスが発信元となります。

|ロジック アプリのリージョン|送信 IP|
|-----|----|
|オーストラリア東部|40.126.251.213|
|オーストラリア南東部|40.127.80.34|
|ブラジル南部|191.232.38.129|
|インド中部|104.211.98.164|
|米国中央部|40.122.49.51|
|東アジア|23.99.116.181|
|米国東部|191.237.41.52|
|米国東部 2|104.208.233.100|
|東日本|40.115.186.96|
|西日本|40.74.130.77|
|米国中北部|65.52.218.230|
|北ヨーロッパ|104.45.93.9|
|米国中南部|104.214.70.191|
|東南アジア|13.76.231.68|
|インド南部|104.211.227.225|
|西ヨーロッパ|40.115.50.13|
|インド西部|104.211.161.203|
|米国西部|104.40.51.248|


## <a name="next-steps"></a>次のステップ  

- Logic Apps の使用を開始するには、 [ロジック アプリの作成](app-service-logic-create-a-logic-app.md) に関するページのチュートリアルに従ってください。  
- [一般的な例とシナリオを確認する](app-service-logic-examples-and-scenarios.md)
- [Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Logic Apps を使用してシステムを統合する方法を説明する](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


