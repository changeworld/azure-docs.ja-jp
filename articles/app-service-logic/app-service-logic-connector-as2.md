---
title: Logic Apps での AS2 コネクタの使用 | Microsoft Docs
description: AS2 コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajeshramabathiran
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/23/2016
ms.author: rajram

---
# AS2 コネクタの使用開始とロジック アプリへの追加
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

AS2 コネクタを使って、AS2 (Applicability Statement 2) トランスポート プロトコルにより企業間通信でメッセージの送受信を行います。データはインターネット経由で安全かつ確実に転送されます。セキュリティはデジタル証明書と暗号化を使用して実現されています。

AS2 コネクタをビジネス ワークフローに追加し、ロジック アプリ内の企業間ワークフローの一部としてデータを処理できます。

## トリガーとアクション
トリガーは、パートナーからの AS2 メッセージの着信などの特定のイベントに応じて新しいインスタンスを起動します。アクションは、AS2 メッセージを受け取った後に、AS2 を使用してこのメッセージを送信するなどの結果を指します。

AS2 コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。AS2 コネクタでは、次のトリガーとアクションを使用できます。

| トリガー | アクション |
| --- | --- |
| Receive & Decode |エンコードと送信 |

## 前提要件
AS2 コネクタで使用する前に、次の項目を自分で作成する必要があります。

| 要件 | Description |
| --- | --- |
| TPM API アプリ |AS2 コネクタを作成する前に、[BizTalk 取引先管理コネクタ][1]を作成する必要があります。<br/><br/>**注:** 使用する TPM API アプリの名前を把握しておいてください。 |
| Azure SQL データベース |パートナー、スキーマ、証明書、契約などの B2B 項目を格納します。B2B API Apps ごとに専用の Azure SQL Database が必要です。<br/><br/>**注:** このデータベースへの接続文字列をコピーしてください。<br/><br/>[Azure SQL Database の作成](../sql-database/sql-database-get-started.md) |
| Azure BLOB Storage コンテナー |AS2 アーカイブが有効な場合に、メッセージのプロパティを格納します。AS2 メッセージをアーカイブする必要がない場合は、Storage コンテナーは必要ありません。<br/><br/>**注:** アーカイブを有効にしている場合は、この BLOB Storage への接続文字列をコピーしてください。<br/><br/>[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md) |

## AS2 コネクタの作成
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "AS2 コネクタ" を検索して選択し、**[作成]** を選択します。
3. 名前、App Service プラン、その他のプロパティを入力します。
4. 次のパッケージの設定を入力します。
   
   | プロパティ | 説明 |
   | --- | --- |
   | データベース接続文字列 |作成した Azure SQL Database への ADO.NET 接続文字列を入力します。接続文字列をコピーしても、パスワードは接続文字列に追加されません。接続文字列を貼り付ける前に、接続文字列にパスワードを必ず入力してください。 |
   | 受信メッセージのアーカイブを有効にする |省略可能。このプロパティを有効にすると、パートナーから受け取った受信 AS2 メッセージのメッセージ プロパティが格納されます。 |
   | Azure BLOB Storage の接続文字列 |作成した Azure BLOB Storage コンテナーへの接続文字列を入力します。アーカイブが有効な場合は、エンコードまたはデコードされたメッセージがこの Storage コンテナーに格納されます。 |
   | TPM インスタンス名 |前に作成した **BizTalk 取引先管理** API アプリの名前を入力します。AS2 コネクタを作成すると、このコネクタではこの特定の TPM インスタンス内の AS2 契約のみを実行します。 |
5. **[作成]** を選択します。

取引先は、B2B (企業間) コミュニケーションに含まれるエンティティです。2 つのパートナーがある関係を確立した場合、この関係は "契約" と呼ばれます。契約は、2 つのパートナーが達成したいと考えているコミュニケーションに基づいて定義され、プロトコルまたはトランスポートに固有のものです。

[取引先契約の作成][2]に必要な手順をご確認ください。

## コネクタをトリガーとして使用する
1. ロジック アプリを作成または編集する場合は、作成した AS2 コネクタを右側のウィンドウから選択します。![トリガー設定][3]
2. 右矢印 (→) をクリックします。![トリガー オプション][4]
3. AS2 コネクタでは 1 つのトリガーが表示されます。*[受信してデコード]* を選択します。![入力の受信およびデコード][5]
4. このトリガーには、入力がありません。右矢印 (→) をクリックします。![受信およびデコードの構成][6]

出力の一部として、コネクタは、AS2 固有のメタデータだけでなく AS2 ペイロードも返します。

トリガーは、AS2 ペイロードが https://{Host URL}/decode への POST であるときに起動されます。Host URL は、API アプリ設定で確認できます。また、アプリケーション設定で API アプリのアクセス レベルをパブリック (認証済みまたは匿名) に変更する必要があります。

## コネクタをアクションとして使用する
1. トリガーの後ろに (または [このロジックを手動で実行] を選択して)、作成した AS2 コネクタを右側のウィンドウから追加します。![アクションの設定][7]
2. 右矢印 (→) をクリックします。![アクションの一覧][8]
3. AS2 のコネクタはアクションを 1 つのみサポートします。*[エンコードして送信]* を選択します。![入力のエンコードおよび送信][9]
4. アクション用のデータを入力して構成します。![エンコードおよび送信の構成][10]
   
    パラメーターは、次のとおりです。
   
   | パラメーター | 型 | Description |
   | --- | --- | --- |
   | ペイロード |オブジェクト |エンコードし、構成済みのエンドポイントに投稿するペイロードの内容。ペイロードは、JSON オブジェクトとして指定する必要があります。 |
   | AS2 の送信元 |string |AS2 メッセージの送信元の AS2 の ID。このパラメーターは、メッセージを送信するための適切な契約の参照に使用されます。 |
   | AS2 の宛先 |string |AS2 メッセージの受信側の AS2 の ID。このパラメーターは、メッセージを送信するための適切な契約の参照に使用されます。 |
   | パートナーの URL |string |送信する必要があるメッセージの宛先となるパートナーのエンド ポイント。 |
   | アーカイブの有効化 |boolean |送信メッセージをアーカイブする必要があるかどうかを判断します。 |

アクションが正常に終了すると、HTTP 200 の応答コードを返します。

## コネクタでできること
[AS2 メッセージをアーカイブ](app-service-logic-archive-as2-messages.md)できます。

ロジック アプリの詳細については、「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

> [!NOTE]
> Azure アカウントにサインアップせずに Azure Logic Apps を体験したい場合は、[Logic Apps の試用](https://tryappservice.azure.com/?appservice=logic)に関するページをご覧ください。App Service で短時間有効な簡易版のロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=AcomDC_0824_2016-->