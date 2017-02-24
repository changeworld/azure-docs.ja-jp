---
title: "Azure Logic Apps で X12 メッセージをエンコードする | Microsoft Docs"
description: "ロジック アプリで Enterprise Integration Pack の X12 エンコーダーを使用する方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>Encode X12 Message を使ってみる
EDI およびパートナー固有のプロパティを検証したり、XML エンコード メッセージをインターチェンジの EDI トランザクション セットに変換したり、技術確認または機能確認を要求したりできます。

## <a name="prereqs"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* Encode X12 Message コネクタを使用するには、統合アカウントが必要です。 [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)、[パートナー](logic-apps-enterprise-integration-partners.md)、および [X12 契約](logic-apps-enterprise-integration-x12.md)の作成方法の詳細を確認してください。

## <a name="encode-x12-messages"></a>X12 メッセージをエンコードする

1. [ロジック アプリを作成](logic-apps-create-a-logic-app.md)します。
2. このコネクタにはトリガーがありません。 ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。  Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。  ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「x12」と入力します。  [X12 - Encode X12 Message by agreement name (X12 - 契約名による Encode X12 Message)] または [X12 - Encode to X12 message by identities (X12 - ID による Encode to X12 message)] を選択します。  
   
    ![search x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。
   
    ![integration account connection](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. 統合アカウントの詳細を入力します。  アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | 接続名 * |接続の任意の名前を入力します。 |
   | 統合アカウント * |統合アカウント名を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |
   
    入力が完了すると、接続の詳細は次のようになります。
   
    ![integration account connection created](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. **[作成]**を選択します。
6. 接続が作成されたことを確認します。
   
    ![integration account connection details](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>契約名に基づく X12 メッセージのエンコード
ドロップダウンから X12 契約を選択し、エンコードする XML メッセージを選択します。     
    ![provide mandatory fields](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>ID に基づく X12 メッセージのエンコード
送信者の識別子、送信者の修飾子、受信者の識別子、受信者の修飾子を、X12 契約で構成されているとおりに入力します。  エンコードする XML メッセージを選択します。
   
   ![provide mandatory fields](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 エンコードの詳細

X12 Encode コネクタは次の処理を行います。

* 送信者と受信者のコンテキスト プロパティの照合による契約解決
* XML エンコード メッセージをインターチェンジ内の EDI トランザクション セットに変換して、EDI インターチェンジをシリアル化する
* トランザクション セット ヘッダーおよびトレーラー セグメントを適用する
* 各送信インターチェンジのインターチェンジ制御番号、グループ制御番号、およびトランザクション セット制御番号を生成する
* ペイロード データの区切り記号を置換する
* EDI およびパートナー固有のプロパティを検証する
  * メッセージ スキーマと照らし合わせた、トランザクション セット データ要素のスキーマ検証
  * トランザクション セット データ要素に対して実行される EDI 検証
  * トランザクション セット データ要素に対して実行される拡張検証
* 技術確認または機能確認を要求する (構成されている場合)
  * 技術確認は、ヘッダー検証の結果として生成されます。 技術確認は、アドレス受信者によるインターチェンジ ヘッダーとトレーラーの処理の状態を報告します。
  * 機能確認は、本文検証の結果として生成されます。 機能確認は、受信したドキュメントの処理中に発生した各エラーを報告します。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") 




<!--HONumber=Jan17_HO5-->


