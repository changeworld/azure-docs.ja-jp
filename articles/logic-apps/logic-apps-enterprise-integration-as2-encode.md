---
title: "Azure Logic Apps で AS2 メッセージをエンコードする | Microsoft Docs"
description: "Enterprise Integration Pack に含まれる AS2 エンコーダーと Logic Apps の使用方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>Encode AS2 Message を使ってみる
Encode AS2 Message に接続すると、メッセージを転送するときに、セキュリティと信頼性を確保できます。 デジタル署名、暗号化、およびメッセージ処理通知 (MDN) による受信確認が可能になります。これにより、否認不可もサポートできるようになります。

## <a name="prereqs"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* Encode AS2 Message コネクタを使用するには、統合アカウントが必要です。 [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)、[パートナー](logic-apps-enterprise-integration-partners.md)、および [AS2 契約](logic-apps-enterprise-integration-as2.md)の作成方法の詳細を確認してください。

## <a name="encode-as2-messages"></a>AS2 メッセージをエンコードする
1. [ロジック アプリを作成](logic-apps-create-a-logic-app.md)します。
2. このコネクタにはトリガーがありません。 ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。  Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。  ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「AS2」と入力します。  [AS2 – Encode AS2 Message] (AS2 – AS2 メッセージのエンコード) を選択します。
   
    ![search AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。
   
    ![create connection to integration account](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. 統合アカウントの詳細を入力します。  アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | 接続名 * |接続の任意の名前を入力します。 |
   | 統合アカウント * |統合アカウント名を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |
   
      入力が完了すると、接続の詳細は次のようになります。
   
      ![integration connection established](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. **[作成]**を選択します。
6. 接続が作成されたことを確認します。  AS2-From 識別子と AS2-To 識別子を契約で構成されているとおりに入力し、本文 (メッセージ ペイロード) の詳細を指定します。
   
    ![provide mandatory fields](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2 エンコーダーの詳細
Encode AS2 コネクタは次の処理を行います。 

* AS2/HTTP ヘッダーを適用する
* 送信メッセージに署名する (構成されている場合)
* 送信メッセージを暗号化する (構成されている場合)
* メッセージを圧縮する (構成されている場合)

## <a name="try-it-yourself"></a>自分で試してみる
実際にやってみましょう。 [AS2 ロジック アプリのテンプレートとシナリオ](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)を使用して、完全に動作するロジック アプリをデプロイしてください。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") 




<!--HONumber=Jan17_HO5-->


