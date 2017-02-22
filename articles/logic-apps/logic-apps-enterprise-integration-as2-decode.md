---
title: "Azure Logic Apps で AS2 メッセージをデコードする | Microsoft Docs"
description: "Enterprise Integration Pack に含まれる AS2 デコーダーと Logic Apps の使用方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 37562ff385305088590c793147b8ad268148c40b


---
# <a name="get-started-with-decoding-as2-message-in-your-logic-apps"></a>ロジック アプリでの AS2 メッセージのデコードの概要
Decode AS2 Message に接続すると、メッセージを転送するときに、セキュリティと信頼性を確保できます。 デジタル署名、解読、およびメッセージ処理通知 (MDN) による受信確認が可能になります。

## <a name="prereqs"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* Decode AS2 Message コネクタを使用するには、統合アカウントが必要です。 [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)、[パートナー](logic-apps-enterprise-integration-partners.md)、および [AS2 契約](../logic-apps/logic-apps-enterprise-integration-as2.md)の作成方法の詳細を確認してください。

## <a name="decode-as2-messages"></a>AS2 メッセージをデコードする
1. [ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)。
2. このコネクタにはトリガーがありません。 ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。  Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。  ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「AS2」と入力します。  [AS2 – Decode AS2 Message] (AS2 – AS2 メッセージのデコード) を選択します。
   
    ![Search AS2](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)
3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。
   
    ![Create integration connection](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)
4. 統合アカウントの詳細を入力します。  アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | 接続名 * |接続の任意の名前を入力します。 |
   | 統合アカウント * |統合アカウント名を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |
   
      入力を完了すると、接続の詳細は次のようになります。
   
      ![integration connection](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)
5. **[作成]**を選択します。
6. 接続が作成されたことを確認します。  これで、ロジック アプリで他の手順に進むことができるようになりました。
   
    ![integration connection created](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 
7. 要求の出力から本文とヘッダーを選択します。
   
    ![provide mandatory fields](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>AS2 デコーダーの詳細
Decode AS2 コネクターは次の処理を行います。 

* AS2/HTTP ヘッダーを処理する
* 署名を検証する (構成されている場合)
* メッセージを解読する (構成されている場合)
* メッセージを展開する (構成されている場合)
* 受信した MDN と元の送信メッセージを調整する
* 否認不可データベースのレコードを更新し、関連付ける
* AS2 状態レポート用のレコードを書き込む
* 出力ペイロードの内容は、base64 でエンコードされる
* AS2 契約の構成に基づいて、MDN が必要かどうかや、MDN を同期にするか非同期にするかを判断する
* 同期または非同期の MDN を生成する (契約の構成に基づいて)
* MDN の関連付けトークンとプロパティを設定する

## <a name="try-it-yourself"></a>自分で試してみる
実際にやってみましょう。 [AS2 ロジック アプリのテンプレートとシナリオ](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)を使用して、完全に動作するロジック アプリをデプロイしてください。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md) 




<!--HONumber=Jan17_HO5-->


