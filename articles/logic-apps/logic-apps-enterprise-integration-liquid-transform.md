---
title: "Liquid 変換を使用した JSON データの変換 - Azure Logic Apps | Microsoft Docs"
description: "Logic Apps と Liquid テンプレートを使用して、高度な JSON 変換用の変換またはマップを作成します。"
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>Liquid テンプレートを使用した高度な JSON 変換
Azure Logic Apps は、Compose や Parse JSON などのネイティブのデータ操作アクションを通じて、基本的な JSON 変換をサポートします。 Logic Apps では、Liquid テンプレートを使用した高度な JSON 変換もサポートされるようになりました。 [Liquid](https://shopify.github.io/liquid/) は、柔軟な Web アプリのためのオープン ソースのテンプレート言語です。
 
この記事では、反復、制御フロー、変数など、より複雑な JSON 変換をサポートできる、Liquid マップまたはテンプレートの使用方法について説明します。 ロジック アプリで Liquid 変換を実行するには、この Liquid マップで JSON 間のマッピングを定義し、そのマップを統合アカウントに保存しておく必要があります。

## <a name="prerequisites"></a>前提条件
Liquid アクションを使用するための前提条件は次のとおりです。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金のサブスクリプションにサインアップ](https://azure.microsoft.com/pricing/purchase-options/)することもできます。

* [ロジック アプリの作成方法](../logic-apps/logic-apps-create-a-logic-app.md)に関する基本的な知識。

* Basic [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>Liquid テンプレートまたはマップを作成して統合アカウントに追加する

1. この例のサンプル Liquid テンプレートを作成します。 この Liquid テンプレートでは、次に示すように、JSON 入力の変換方法を定義します。

   ```
   {%- assign deviceList = content.devices | Split: ', ' -%}
    {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
    }
    ```
    > [!NOTE]
    > Liquid テンプレートで[フィルター](https://shopify.github.io/liquid/basics/introduction/#filters)を使用する場合、それらのフィルターは大文字で始まる必要があります。 

2. [Azure ポータル](https://portal.azure.com)にサインインします。

3. Azure のメイン メニューで、**[すべてのリソース]** を選択します。 

4. 検索ボックスに統合アカウントを入力します。 アカウントを選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  統合アカウント タイルで、**[マップ]** を選択します。

   ![マップを選択する](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. **[追加]** を選択し、マップの詳細を入力します。
  * **[名前]**: マップの名前。この例では "JsontoJsonTemplate" です。
  * **[マップの種類]**: マップの種類。 JSON から JSON への変換では、**[liquid]** を選択する必要があります。
  * **[マップ]**: 変換に使用する既存の Liquid テンプレートまたはマップ ファイル。この例では "SimpleJsonToJsonTemplate.liquid" です。 ファイル ピッカーを使用して、このファイルを探すことができます。

    ![Liquid テンプレートを追加する](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>Liquid アクションを追加してロジック アプリで JSON を変換する

1. [ロジック アプリを作成](logic-apps-create-a-logic-app.md)します。

2. ロジック アプリに [Request トリガー](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)を追加します。

3. **[+ 新しいステップ] > [アクションの追加]** を選択します。 検索ボックスで *liquid* を検索します。 **[Liquid - JSON を JSON に変換]** を選択します。

  ![検索-アクション-liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. **[コンテンツ]** ボックスで、動的コンテンツ一覧またはパラメーター一覧 (どちらか表示された方) から **[本文]** を選択します。 
  
  ![本文を選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. **[マップ]** ドロップダウン リストから Liquid テンプレートを選択します。この例では "JsonToJsonTemplate" です。

  ![マップを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   一覧が空の場合は、ロジック アプリが統合アカウントにリンクされていないと考えられます。 Liquid テンプレートまたはマップが含まれた統合アカウントにロジック アプリをリンクするには、次の手順に従います。

   1. ロジック アプリのメニューで、**[ワークフロー設定]** を選択します。 
   2. **[統合アカウントを選択してください]** の一覧から統合アカウントを選択し、**[保存]** をクリックします。

     ![ロジック アプリを統合アカウントにリンクする](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>ロジック アプリをテストする

   [Postman](https://www.getpostman.com/postman) などのツールからロジック アプリに JSON 入力を送信します。 ロジック アプリからの変換された JSON 出力は、次の例のようになります。
  
   ![出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  
* [マップについての詳細情報](../logic-apps/logic-apps-enterprise-integration-maps.md "Enterprise Integration のマップについての詳細情報")  

