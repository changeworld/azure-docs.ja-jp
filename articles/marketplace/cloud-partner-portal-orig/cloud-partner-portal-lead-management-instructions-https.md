---
title: HTTPS エンドポイント | Microsoft Docs
description: HTTPS 対応に潜在顧客管理を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807149"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS エンドポイントを使用して潜在顧客管理を構成する

HTTPS エンドポイントを使用して、CRM システムに書き込むことができる Azure Marketplace と AppSource の潜在顧客を処理することができます。 この記事では、Microsoft Flow オートメーション サービスを使用して潜在顧客管理を構成する方法について説明します。


## <a name="create-a-flow-using-microsoft-flow"></a>Microsoft Flow を使用してフローを作成する

1.  [Flow](https://flow.microsoft.com/) の Web ページを開きます。 **[サインイン]** または **[無料でサインアップ]** を選択して、無料の Flow アカウントを作成します。

2.  サインインし、メニュー バーの **[マイ フロー]** を選択します。

    ![マイ フロー](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  **[一から作成]** を選択します。

    ![一から作成する](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  **[要求/応答]** コネクタを選択し、要求トリガーを検索します。 

    ![一から作成する](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. **[要求]** トリガーを選択します。
    ![要求トリガー](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  この記事の最後にある **JSON の例**を **[要求本文の JSON スキーマ]** にコピーします。

7.  新しいステップを追加し、新しいレコードを作成するアクションを含む任意の CRM システムを選択します。 次の画面キャプチャでは、例として **[Dynamics 365 - 新しいレコードの作成]** が示されています。

    ![新しいレコードを作成する](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  コネクタに接続入力を提供し、**[潜在顧客]** エンティティを選択します。

    ![潜在顧客を選択する](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  潜在顧客の情報を提供するためのフォームが表示されます。 動的なコンテンツの追加を選択して、入力要求からの項目をマップできます。

    ![動的なコンテンツの追加](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  必要なフィールドをマップし、**[保存]** を選択してフローを保存します。

11. [要求] に HTTP POST URL が作成されます。 この URL をコピーし、HTTPS エンドポイントとして使用します。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する

オファー用の潜在顧客管理情報を構成するときは、[Lead Destination]\(リードの宛先\) で **[HTTPS エンドポイント]** を選択し、前のステップでコピーした HTTP POST URL を貼り付けます。  

![動的なコンテンツの追加](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

潜在顧客が生成されると、Flow に送信されてから、構成した CRM システムにルーティングされます。


## <a name="json-example"></a>JSON の例

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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
