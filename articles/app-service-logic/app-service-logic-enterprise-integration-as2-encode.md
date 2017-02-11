---
title: "Enterprise Integration Pack の Encode AS2 Message コネクタの詳細情報 | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps を使用してパートナーを使用する方法について説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ebfefbafe6a47beb406bbea0f7c48b61a37ac151


---
# <a name="get-started-with-encode-as2-message"></a>Encode AS2 Message を使ってみる
Encode AS2 Message に接続すると、メッセージを転送するときに、セキュリティと信頼性を確保できます。 デジタル署名、暗号化、およびメッセージ処理通知 (MDN) による受信確認が可能になります。これにより、否認不可もサポートできるようになります。

## <a name="create-the-connection"></a>接続の作成
### <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* Encode AS2 Message コネクタを使用するには、統合アカウントが必要です。 [統合アカウント](app-service-logic-enterprise-integration-create-integration-account.md)、[パートナー](app-service-logic-enterprise-integration-partners.md)、および [AS2 契約](app-service-logic-enterprise-integration-as2.md)の作成方法の詳細を確認してください。

### <a name="connect-to-encode-as2-message-using-the-following-steps"></a>次の手順に従って、Encode AS2 Message に接続します。
1. [ロジック アプリの作成](app-service-logic-create-a-logic-app.md)に関する記事に例が記載されています。
2. このコネクタにはトリガーがありません。 ロジック アプリを起動するには、他のトリガー (要求トリガーなど) を使用します。  Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。  ドロップダウン リストから [Microsoft が管理している API を表示] を選択し、検索ボックスに「AS2」と入力します。  [AS2 – Encode AS2 Message] を選択します。
   
    ![search AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. これまでに統合アカウントへの接続を作成したことがない場合は、接続の詳細情報を求められます。
   
    ![create connection to integration account](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)  
4. 統合アカウントの詳細を入力します。  アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | 接続名 * |接続の任意の名前を入力します。 |
   | 統合アカウント * |統合アカウント名を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |
   
      入力を完了すると、接続の詳細は次のようになります。
   
      ![integration connection established](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)  
5.  **[作成]**
6. 接続が作成されたことを確認します。  AS2-From、AS2-To 識別子を契約で構成されているとおりに指定し、本文 (メッセージ ペイロード) の詳細を指定します。 
   
    ![provide mandatory fields](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## <a name="the-as2-encode-does-the-following"></a>AS2 Encode の機能
* AS2/HTTP ヘッダーを適用する
* 送信メッセージに署名する (構成されている場合)
* 送信メッセージを暗号化する (構成されている場合)
* メッセージを圧縮する (構成されている場合)

## <a name="try-it-for-yourself"></a>試してみる
実際に試してみましょう。 Logic Apps AS2 の機能を使用して、本格的な機能を備えた独自のロジック アプリをデプロイするには、 [こちら](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) をクリックしてください。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") 




<!--HONumber=Nov16_HO3-->


