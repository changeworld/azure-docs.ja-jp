---
title: "Enterprise Integration Pack の Decode X12 Message コネクタの詳細情報 | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps を使用してパートナーを使用する方法について説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: b6ce911b8435f3d6ed1c12c78ba81abe180346a2


---
# <a name="get-started-with-decode-x12-message"></a>Decode X12 Message を使ってみる
EDI およびパートナー固有のプロパティを検証したり、各トランザクション セットに対して XML ドキュメントを生成したり、処理したトランザクションの受信確認を生成したりできます。

## <a name="create-the-connection"></a>接続の作成
### <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* Decode X12 Message コネクタを使用するには、統合アカウントが必要です。 [統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)、[パートナー](../logic-apps/logic-apps-enterprise-integration-partners.md)、および [X12 契約](../logic-apps/logic-apps-enterprise-integration-x12.md)の作成方法の詳細を確認してください。

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>次の手順に従って、Decode X12 Message に接続します。
1. [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事に例が記載されています。
2. このコネクタにはトリガーがありません。 ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。  Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。  ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「x12」と入力します。  [X12 – Decode X12 Message] を選択します。
   
    ![search x12](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  
3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。
   
    ![integration account connection](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)     
4. 統合アカウントの詳細を入力します。  アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | 接続名 * |接続の任意の名前を入力します。 |
   | 統合アカウント * |統合アカウント名を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |
   
    入力を完了すると、接続の詳細は次のようになります。
   
    ![integration account connection created](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 
5. **[作成]**
6. 接続が作成されたことを確認します。
   
    ![integration account connection details](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 
7. デコードする X12 フラット ファイル メッセージを選択します。
   
    ![provide mandatory fields](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>X12 Decode の機能
* 取引パートナー契約と照らし合わせてエンベロープを検証する
* 各トランザクション セットの XML ドキュメントを生成する
* EDI およびパートナー固有のプロパティを検証する
  * EDI 構造検証、および拡張スキーマ検証
  * インターチェンジ エンベロープの構造の検証
  * 制御スキーマと照らし合わせたエンベロープのスキーマ検証
  * メッセージ スキーマと照らし合わせたトランザクション セット データ要素のスキーマ検証
  * トランザクション セット データ要素に対して実行される EDI 検証 
* インターチェンジ、グループ、およびトランザクション セットの制御番号が重複していないことを検証する
  * 以前に受信したインターチェンジと照らし合わせて、インターチェンジ制御番号を確認する。
  * インターチェンジ内の他のグループ制御番号と照らし合わせて、グループ制御番号を確認する。
  * グループ内の他のトランザクション セット制御番号と照らし合わせて、トランザクション セット制御番号を確認する。
* インターチェンジ全体を XML に変換する 
  * [インターチェンジをトランザクション セットとして分割 - エラー発生時にトランザクション セットを中断]: インターチェンジの各トランザクション セットを個別の XML ドキュメントとして解析します。 インターチェンジの&1; つ以上のトランザクション セットが検証に失敗した場合、X12 Decode は失敗したトランザクション セットのみを中断します。
  * [インターチェンジをトランザクション セットとして分割 - エラー発生時にインターチェンジを中断]: インターチェンジの各トランザクション セットを個別の XML ドキュメントとして解析します。  インターチェンジの&1; つ以上のトランザクション セットが検証に失敗した場合、X12 Decode はインターチェンジ全体を中断します。
  * [インターチェンジの保存 - エラー発生時にトランザクション セットを中断]: バッチ インターチェンジ全体に対する XML ドキュメントを作成します。 X12 Decode は、検証に失敗したトランザクション セットだけを中断し、他のすべてのトランザクション セットの処理を継続します。
  * [インターチェンジの保存 - エラー発生時にインターチェンジを中断]: バッチ インターチェンジ全体に対する XML ドキュメントを作成します。 インターチェンジの&1; つ以上のトランザクション セットが検証に失敗した場合、X12 Decode はインターチェンジ全体を中断します。 
* 技術確認または機能確認を生成する (構成されている場合)
  * 技術確認は、ヘッダー検証の結果として生成されます。 技術確認は、アドレス受信者によるインターチェンジ ヘッダーとトレーラーの処理の状態を報告します。
  * 機能確認は、本文検証の結果として生成されます。 機能確認は、受信したドキュメントの処理中に発生した各エラーを報告します。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") 




<!--HONumber=Jan17_HO3-->


