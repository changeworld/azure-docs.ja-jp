---
title: "Enterprise Integration Pack の AS2 契約の作成についての詳細情報"
description: "Enterprise Integration Pack の AS2 契約の作成についての詳細情報| Microsoft Azure App Service"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13b2757a10c6da7ca3dd14c3db9620fab0035561


---
# <a name="enterprise-integration-with-as2"></a>AS2 でのエンタープライズ統合
## <a name="create-an-as2-agreement"></a>AS2 契約の作成
エンタープライズ機能を Logic Apps で使用するには、最初に契約を作成する必要があります。 

### <a name="heres-what-you-need-before-you-get-started"></a>開始する前に必要な項目
* Azure サブスクリプションで定義されている[統合アカウント](app-service-logic-enterprise-integration-accounts.md)  
* 統合アカウントで既に定義されている 2 つ以上の [パートナー](app-service-logic-enterprise-integration-partners.md)  

> [!NOTE]
> 契約を作成する場合は、契約のファイルの内容が契約の種類と一致している必要があります。    
> 
> 

[統合アカウントを作成](app-service-logic-enterprise-integration-accounts.md)し、[パートナーを追加](app-service-logic-enterprise-integration-partners.md)した後に、契約を作成するには、次の手順に従います。  

### <a name="from-the-azure-portal-home-page"></a>Azure ポータルのホーム ページから
 [Azure ポータル](http://portal.azure.com "Azure ポータル")にログインしたら、次の手順に従います。  

1. 左側のメニューの **[参照]** を選択します。  

> [!TIP]
> **[参照]** リンクが表示されない場合は、最初にメニューを展開する必要があります。 これを行うには、折りたたまれたメニューの左上にある **[メニューの表示]** リンクを選択します。  
> 
> 

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

1. フィルター検索ボックスに「 *integration* 」と入力し、結果のリストから **[統合アカウント]** を選択します。       
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
2. 表示された **[統合アカウント]** ブレードで、契約を作成する統合アカウントを選択します。 統合アカウントがリストに表示されない場合は、[最初に統合アカウントを 1 つ作成](app-service-logic-enterprise-integration-accounts.md "All about integration accounts")します。  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
3. **[契約]** タイルを選択します。 契約タイルが表示されない場合は、最初に契約タイルを追加します。   
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
4. 開かれた [契約] ブレードの **[追加]** ボタンを選択します。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
5. [契約] ブレードが開いたら、契約の**名前**を入力し、**ホスト パートナー**、**ホスト ID**、**ゲスト パートナー**、**ゲスト ID** を選択します。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

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
   
   * 必要に応じて、受信メッセージのプロパティをオーバーライドできます。 これを行うには、 **[Override message properties (メッセージのプロパティをオーバーライドする)]** チェックボックスをオンにします。
   * すべての受信メッセージに署名が必要な場合は、 **[Message should be signed (メッセージに署名を必ず付ける)]** チェックボックスをオンにします。 このオプションを選択する場合は、メッセージ上の署名の検証に使用する **証明書** を選択する必要があります。
   * 必要に応じて、メッセージの暗号化も必須にすることができます。 これを行うには、 **[Message should be encrypted (メッセージは必ず暗号化する)]** チェックボックスをオンします。 次に、受信メッセージのデコードに使用する **証明書** を選択する必要があります。
   * メッセージの圧縮を必須にすることもできます。 これを行うには、 **[Message should be compressed (メッセージは必ず圧縮する)]** チェックボックスをオンします。  
     ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

各受信設定で何が有効になるかを調べる必要がある場合は、次の表を参照してください。  

| プロパティ | 説明 |
| --- | --- |
| [Override message properties (メッセージのプロパティをオーバーライドする)] |これをオンにすると、受信メッセージのプロパティがオーバーライドできることが示されます。 |
| [Message should be signed (メッセージに署名を必ず付ける)] |これをオンにすると、メッセージへのデジタル署名が必須になります。 |
| [Message should be encrypted (メッセージは必ず暗号化する)] |これをオンにすると、メッセージの暗号化が必須になります。 暗号化されていないメッセージは拒否されます。 |
| [Message should be compressed (メッセージは必ず圧縮する)] |これをオンにすると、メッセージの圧縮が必須になります。 圧縮されていないメッセージは拒否されます。 |
| MDN Text (MDN テキスト) |これはメッセージの送信者に送信される既定の MDN です。 |
| Send MDN (MDN を送信する) |これをオンにすると、MDN を送信できるようになります。 |
| Send signed MDN (署名付き MDN を送信する) |これをオンにすると、MDN への署名が必須になります。 |
| MIC Algorithm (MIC アルゴリズム) | |
| Send asynchronous MDN (非同期 MDN を送信する) |これをオンにすると、メッセージの非同期送信が必須になります。 |
| URL |メッセージの送信先の URL です。 |

説明を続けます。  

1. **[Send Settings (送信の設定)]** を選択し、この契約を使用して送信されたメッセージを処理する方法を構成します。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

各送信設定で何が有効になるかを調べる必要がある場合は、次の表を参照してください。  

| プロパティ | 説明 |
| --- | --- |
| Enable message signing (メッセージの署名を有効にする) |このチェックボックスをオンにすると、契約から送信されたすべてのメッセージへの署名が有効になります。 |
| MIC Algorithm (MIC アルゴリズム) |メッセージの署名に使用するアルゴリズムを選択します。 |
| 証明書 |メッセージの署名に使用する証明書を選択します。 |
| Enable message encryption (メッセージの暗号化を有効にする) |このチェック ボックスをオンにすると、この契約から送信されたすべてのメッセージが暗号化されます。 |
| Encryption Algorithm (暗号化アルゴリズム) |メッセージの暗号化に使用する暗号化アルゴリズムを選択します。 |
| Unfold HTTP headers (HTTP ヘッダーを展開する) |このチェック ボックスをオンにすると、HTTP Content-type ヘッダーが 1 行に展開されます。 |
| Request MDN (MDN を要求する) |このチェックボックスをオンにすると、この契約から送信されたすべてのメッセージの MDN を要求します。 |
| Request signed MDN (署名付き MDN を要求する) |オンにすると、この契約に送信されるすべての MDN への署名が必須になります。 |
| Request asynchronous MDN (非同期 MDN を要求する) |オンにすると、この契約への非同期 MDN の送信が必須になります。 |
| URL |MDN の送信先の URLです。 |
| Enable NRR (NRR を有効にする) |このチェック ボックスをオンにすると、Non-Repudiation of Receipt が有効になります。 |

あともう少しで終了です。  

1. [Integration Accounts (統合アカウント)] ブレード上の **[Agreements (契約)]** タイルを選択すると、新しく追加された契約が表示されます。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)




<!--HONumber=Nov16_HO3-->


