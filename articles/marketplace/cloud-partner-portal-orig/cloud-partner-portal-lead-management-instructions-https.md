---
title: HTTPS エンドポイントを使用してリード管理を構成する | Azure Marketplace
description: HTTP エンドポイントを使用して、Microsoft AppSource と Azure Marketplace のリードを処理する方法について説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770181"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS エンドポイントを使用して潜在顧客管理を構成する

HTTP エンドポイントを使用して、Microsoft AppSource と Azure Marketplace のリードを処理できます。 これらのリードは、カスタマー リレーションシップ マネジメント (CRM) システムに書き込んだり、電子メール通知として送信したりすることができます。 この記事では、[Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) オートメーション サービスを使用してリード管理を構成する方法について説明します。

## <a name="create-a-flow-using-microsoft-power-automate"></a>Microsoft Power Automate を使用してフローを作成する

1. [Power Automate](https://flow.microsoft.com/) Web ページを開きます。 **[サインイン]** または **[無料でサインアップ]** を選択して、無料の Flow アカウントを作成します。

1. サインインし、メニュー バーの **[マイ フロー]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![マイ フロー](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. **[+ 新規]** の下で、 **[+ Instant—from blank]\(インスタント - 一から作成\)** を選択します。
    > [!div class="mx-imgBorder"]
    > ![一から作成](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. フローに名前を付け、次に、 **[このフローをトリガーする方法を選択します]** の下で **[HTTP 要求の受信時]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![HTTP 要求の受信トリガーを選択する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. フロー ステップをクリックして展開します。

    > [!div class="mx-imgBorder"]
    > ![フロー ステップを展開する](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. 次のいずれかの方法を使用して、 **[要求本文の JSON スキーマ]** を構成します。

   - この記事の最後にある [JSON スキーマ](#json-schema)を **[要求本文の JSON スキーマ]** テキスト ボックスにコピーします。
   - **[サンプルのペイロードを使用してスキーマを生成する]** を選びます。 **[サンプルの JSON ペイロードを入力するか、貼り付けます]** テキスト ボックスに [JSON の例](#json-example)を貼り付けます。 **[完了]** を選択してスキーマを作成します。

   >[!Note]
   >フローのこの時点で、CRM システムに接続したり、電子メール通知を構成したりすることができます。

### <a name="to-connect-to-a-crm-system"></a>CRM システムに接続するには:

1. **[+ New step (+ 新しいステップ)]** を選択します。
2. 新しいレコードを作成するアクションを含む任意の CRM システムを選択します。 次のスクリーン キャプチャでは、例として **[Dynamics 365 - 新しいレコードの作成]** が示されています。

    ![新しいレコードを作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. コネクタの接続入力である **[組織名]** を入力します。 **[エンティティ名]** ドロップダウン リストから **[潜在顧客]** を選択します。

    ![潜在顧客を選択する](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. 潜在顧客の情報を提供するためのフォームが Flow に表示されます。 動的なコンテンツの追加を選択して、入力要求からの項目をマップできます。 次のスクリーン キャプチャでは、例として **OfferTitle** が示されています。

    ![動的なコンテンツの追加](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. 必要なフィールドをマップし、 **[保存]** を選択してフローを保存します。

6. HTTP POST URL が要求に作成されます。 この URL をコピーし、HTTPS エンドポイントとして使用します。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>電子メール通知を設定するには

1. **[+ New step (+ 新しいステップ)]** を選択します。
2. **[アクションを選択してください]** で **[アクション]** を選択します。
3. **[アクション]** で、 **[電子メールの送信]** を選択します。

    ![電子メール アクションを作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. **[電子メールの送信]** で次の必須フィールドを構成します。

   - **[To]** - 少なくとも 1 つの有効なメール アドレスを入力する必要があります。
   - **[件名]** - 次のスクリーン キャプチャの **LeadSource** のように、Flow には動的なコンテンツを追加するオプションがあります。

     ![動的なコンテンツを使用して電子メール アクションを追加する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **[本文]** - [動的なコンテンツ] 一覧から、電子メールの本文に必要な情報を追加します。 たとえば、LastName、FirstName、Email、Company などです。

   電子メール通知の設定を完了すると、次のスクリーン キャプチャの例のようになります。

   ![電子メール アクションを作成する](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. **[保存]** を選択してフローを終了します。

6. HTTP POST URL が要求に作成されます。 この URL をコピーし、HTTPS エンドポイントとして使用します。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する

オファー用の潜在顧客管理情報を構成するときは、 **[Lead Destination]\(潜在顧客の宛先\)** で **[HTTPS エンドポイント]** を選択し、前のステップでコピーした HTTP POST URL を貼り付けます。  

![動的なコンテンツの追加](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

リードが生成されると、それらのリードは Power Automate フローに送信され、構成した CRM システムまたはメール アドレスにルーティングされます。

## <a name="json-schema-and-example"></a>JSON スキーマと例

この JSON テスト例では、次のスキーマを使用しています。

### <a name="json-schema"></a>JSON スキーマ

``` json
{
  "$schema": "https://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "Description": {
      "id": "/properties/Description",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```

次の JSON の例をコピーして編集し、フローでテストとして使用できます。

### <a name="json-example"></a>JSON の例

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>次のステップ

まだ Cloud パートナー ポータルで[潜在顧客](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)を構成していない場合は構成します。
