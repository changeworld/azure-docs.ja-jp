---
title: "Enterprise Integration Pack の AS2 契約の作成についての詳細情報 | Microsoft Docs"
description: "Enterprise Integration Pack の AS2 契約の作成についての詳細情報 | Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>AS2 でのエンタープライズ統合
## <a name="create-an-as2-agreement"></a>AS2 契約の作成
エンタープライズ機能を Logic Apps で使用するには、最初に契約を作成する必要があります。 

### <a name="heres-what-you-need-before-you-get-started"></a>開始する前に必要な項目
* Azure サブスクリプションで定義されている[統合アカウント](../logic-apps/logic-apps-enterprise-integration-accounts.md)  
* 統合アカウントで既に定義されている&2; つ以上の [パートナー](logic-apps-enterprise-integration-partners.md)  

> [!NOTE]
> 契約を作成する場合は、契約のファイルの内容が契約の種類と一致している必要があります。    
> 
> 

[統合アカウントを作成](../logic-apps/logic-apps-enterprise-integration-accounts.md)し、[パートナーを追加](logic-apps-enterprise-integration-partners.md)した後に、契約を作成するには、次の手順に従います。  

### <a name="from-the-azure-portal-home-page"></a>Azure ポータルのホーム ページから
[Azure Portal](http://portal.azure.com "Azure Portal") にログインしたら、次の手順に従います。  

1. **[その他のサービス]** を選択し、フィルター検索ボックスに「**統合**」と入力します。 結果の一覧から **[統合アカウント]** を選択します。    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. 証明書の追加先となる統合アカウントを選択します。 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. **[契約]** タイルを選択します。 契約タイルが表示されない場合は、最初に契約タイルを追加します。   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. 開かれた [契約] ブレードの **[追加]** ボタンを選択します。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. [契約] ブレードで、契約の**名前**を入力し、**[契約タイプ]** で **[AS2]** を選択し、**ホスト パートナー**、**ホスト ID**、**ゲスト パートナー**、**ゲスト ID** を選択します。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

契約の設定を構成する場合に役立つ、いくつかの詳細を次に示します。 

| プロパティ | 説明 |
| --- | --- |
| Host Partner (ホスト パートナー) |契約には、ホストとゲストの両方のパートナーが必要です。 ホスト パートナーは、契約を構成している組織を表します。 |
| Host Identity (ホスト ID) |ホスト パートナーの ID。 |
| Guest Partner (ゲスト パートナー) |契約には、ホストとゲストの両方のパートナーが必要です。 ゲスト パートナーは、ホスト パートナーと取引している組織を表します。 |
| ゲスト ID |ゲスト パートナーの ID。 |
| Receive Settings (受信の設定) |これらのプロパティは、契約によって受信されたすべてのメッセージに適用されます。 |
| Send Settings (送信の設定) |これらのプロパティは、契約によって送信されたすべてのメッセージに適用されます。 |

説明を続けます。  

1. **[Receive Settings (受信の設定)]** を選択し、この契約を使用して受信されたメッセージを処理する方法を構成します。  
   
   * 必要に応じて、受信メッセージのプロパティをオーバーライドできます。 これを行うには、**[メッセージ プロパティを上書きします]** チェックボックスをオンにします。
   * すべての受信メッセージに署名が必要な場合は、**[メッセージに署名してください]** チェックボックスをオンにします。 このオプションを選択した場合は、メッセージ上の署名の検証に使用する "*ゲスト パートナーのパブリック証明書*" を選択する必要があります。
   * すべての受信メッセージを暗号化する必要がある場合は、**[メッセージは暗号化する必要があります]** チェックボックスをオンにします。  このオプションを選択した場合は、受信メッセージを復号化するための "*ホスト パートナーのプライベート証明書*" を選択する必要があります。
   * メッセージの圧縮を必須にすることもできます。 これを行うには、**[メッセージは圧縮する必要があります]** チェックボックスをオンにします。    
   * 受信したメッセージの同期 MDN を送信するには、**[MDN を送信する]** チェックボックスをオンにします。
   * 受信したメッセージの署名付き MDN を送信するには、**[署名付き MDN を送信する]** チェックボックスをオンにします。
   * 受信したメッセージの非同期 MDN を送信するには、**[非同期 MDN を送信する]** チェックボックスをオンにします。     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

各受信設定で何が有効になるかを調べる必要がある場合は、次の表を参照してください。  

| プロパティ | 説明 |
| --- | --- |
| [Override message properties (メッセージのプロパティをオーバーライドする)] |これをオンにすると、受信メッセージのプロパティがオーバーライドできることが示されます。 |
| [Message should be signed (メッセージに署名を必ず付ける)] |これをオンにすると、メッセージへのデジタル署名が必須になります。  署名の検証に使用するゲスト パートナーのパブリック証明書を構成します。  |
| [Message should be encrypted (メッセージは必ず暗号化する)] |これをオンにすると、メッセージの暗号化が必須になります。 暗号化されていないメッセージは拒否されます。 メッセージの復号化に使用するホスト パートナーのプライベート証明書を構成します。  |
| [Message should be compressed (メッセージは必ず圧縮する)] |これをオンにすると、メッセージの圧縮が必須になります。 圧縮されていないメッセージは拒否されます。 |
| MDN Text (MDN テキスト) |これはメッセージの送信者に送信される既定の MDN です。 |
| Send MDN (MDN を送信する) |これをオンにすると、MDN を送信できるようになります。 |
| Send signed MDN (署名付き MDN を送信する) |これをオンにすると、MDN への署名が必須になります。 |
| MIC Algorithm (MIC アルゴリズム) | |
| Send asynchronous MDN (非同期 MDN を送信する) |これをオンにすると、メッセージの非同期送信が必須になります。 |
| URL |MDN の送信先の URL です。 |

説明を続けます。  

1. **[送信設定]** を選択し、この契約を使用して送信されたメッセージを処理する方法を構成します。  

   * パートナーに署名付きメッセージを送信するには、**[メッセージの署名を有効にしてください]** チェックボックスをオンにします。 このオプションを選択した場合、メッセージに署名するための "*ホスト パートナーのプライベート証明書の MIC アルゴリズム*" と "*ホスト パートナーのプライベート証明書*" を選択する必要があります。
   * パートナーに暗号化されたメッセージを送信するには、**[メッセージの暗号化を有効にしてください]** チェックボックスをオンにします。 このオプションを選択した場合、メッセージを暗号化するための "*ゲスト パートナーのパブリック証明書のアルゴリズム*" と "*ゲスト パートナーのパブリック証明書*" を選択する必要があります。
   * これを行うには、**[メッセージは圧縮する必要があります]** チェックボックスをオンにします。 
   * HTTP Content-Type ヘッダーを&1; 行に展開するには、**[HTTP のヘッダーを展開する]** チェックボックスをオンにします。 
   * 送信したメッセージの同期 MDN を受け取るには、**[MDN を要求する]** チェックボックスをオンにします。
   * 送信したメッセージの署名付き MDN を受け取るには、**[署名付き MDN を要求する]** チェックボックスをオンにします。
   * 送信したメッセージの非同期 MDN を受け取るには、**[非同期 MDN を要求する]** チェックボックスをオンにします。 このオプションを選択した場合、MDN の送信先 URL を指定する必要があります。  
   * NRR (Non-repudiation of receipt) を有効にするには、**[NRR を有効にする]** を選択します。    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

各送信設定で何が有効になるかを調べる必要がある場合は、次の表を参照してください。  

| プロパティ | 説明 |
| --- | --- |
| Enable message signing (メッセージの署名を有効にする) |これをオンにすると、契約から送信されたすべてのメッセージへの署名が有効になります。 |
| MIC Algorithm (MIC アルゴリズム) |メッセージの署名に使用するアルゴリズムを選択します。 メッセージに署名するための、ホスト パートナーのプライベート証明書の MIC アルゴリズムをを構成します。 |
| 証明書 |メッセージの署名に使用する証明書を選択します。 メッセージに署名するための、ホスト パートナーのプライベート証明書を構成します。 |
| Enable message encryption (メッセージの暗号化を有効にする) |これをオンにすると、この契約から送信されたすべてのメッセージが暗号化されます。 メッセージを暗号化するための、ゲスト パートナーのパブリック証明書のアルゴリズムを構成します。 |
| Encryption Algorithm (暗号化アルゴリズム) |メッセージの暗号化に使用する暗号化アルゴリズムを選択します。 メッセージを暗号化するための、ゲスト パートナーのパブリック証明書を構成します。 |
| Unfold HTTP headers (HTTP ヘッダーを展開する) |これをオンにすると、HTTP Content-Type ヘッダーが&1; 行に展開されます。 |
| Request MDN (MDN を要求する) |これをオンにすると、この契約から送信されるすべてのメッセージの MDN を要求します。 |
| Request signed MDN (署名付き MDN を要求する) |オンにすると、この契約に送信されるすべての MDN への署名が必須になります。 |
| Request asynchronous MDN (非同期 MDN を要求する) |オンにすると、この契約への非同期 MDN の送信が必須になります。 |
| URL |MDN の送信先の URLです。 |
| Enable NRR (NRR を有効にする) |これをオンにすると、Non-Repudiation of Receipt が有効になります。 |

[統合アカウント] ブレード上の **[契約]** タイルを選択すると、新しく追加された契約が表示されます。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  


<!--HONumber=Jan17_HO4-->


