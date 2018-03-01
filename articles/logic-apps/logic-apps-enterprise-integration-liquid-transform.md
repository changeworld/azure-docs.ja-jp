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
ms.openlocfilehash: 4bb003afd757faac675a9af8599a781247717a64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Liquid テンプレートを使用して高度な JSON 変換を実行する

Azure Logic Apps は、**Compose** や **Parse JSON** などのネイティブなデータ操作アクションを通して基本的な JSON 変換をサポートします。 高度な JSON 変換では、ロジック アプリなしで Liquid テンプレートを使用できます。 
[Liquid](https://shopify.github.io/liquid/) は、柔軟な Web アプリのためのオープン ソースのテンプレート言語です。
 
この記事では、反復処理、制御フロー、変数などの、より複雑な JSON 変換をサポートする Liquid マップまたはテンプレートを使用する方法について説明します。 ロジック アプリで Liquid 変換を実行するには、Liquid マップで JSON から JSON へのマッピングを定義し、そのマップを統合アカウントに格納しておく必要があります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントで作業を開始](https://azure.microsoft.com/free/)できます。 また、[従量課金のサブスクリプションにサインアップ](https://azure.microsoft.com/pricing/purchase-options/)することもできます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* Basic [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>統合アカウントの Liquid テンプレートまたはマップを作成する

1. この例のサンプル Liquid テンプレートを作成します。 この Liquid テンプレートでは、次に示すように、JSON 入力の変換方法を定義します。

   ``` json
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

2. [Azure Portal](https://portal.azure.com) にサインインします。

3. Azure のメイン メニューで、**[すべてのリソース]** を選択します。 

4. 検索ボックスで、統合アカウントを見つけて選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  統合アカウント タイルで、**[マップ]** を選択します。

   ![マップを選択する](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. **[追加]** を選択し、マップの詳細を入力します。

   * **[名前]**: マップの名前 (この例では "JsontoJsonTemplate")
   * **[マップの種類]**: マップの種類。 JSON から JSON への変換では、**[liquid]** を選択する必要があります。
   * **[マップ]**: 変換に使用する既存の Liquid テンプレートまたはマップ ファイル。この例では "SimpleJsonToJsonTemplate.liquid" です。 このファイルを見つけるには、ファイル ピッカーを使用できます。

   ![Liquid テンプレートを追加する](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>JSON 変換のための Liquid アクションを追加する

1. [ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

2. ロジック アプリに [Request トリガー](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)を追加します。

3. **[+ 新しいステップ] > [アクションの追加]** を選択します。 検索ボックスに「*liquid*」と入力し、**[Liquid - Transform JSON to JSON] \(Liquid - JSON から JSON への変換)** を選択します。

   ![Liquid アクションを見つけて選択する](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. **[コンテンツ]** ボックスで、動的コンテンツ一覧またはパラメーター一覧 (どちらか表示された方) から **[本文]** を選択します。
  
   ![本文を選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. **[マップ]** 一覧から、Liquid テンプレート (この例では "JsonToJsonTemplate") を選択します。

   ![マップを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   この一覧が空である場合は、ロジック アプリが統合アカウントにリンクされていない可能性があります。 
   Liquid テンプレートまたはマップが含まれた統合アカウントにロジック アプリをリンクするには、次の手順に従います。

   1. ロジック アプリのメニューで、**[ワークフロー設定]** を選択します。
   2. **[統合アカウントを選択してください]** の一覧から統合アカウントを選択し、**[保存]** をクリックします。

   ![ロジック アプリを統合アカウントにリンクする](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

[Postman](https://www.getpostman.com/postman) などのツールからロジック アプリに JSON 入力を送信します。 ロジック アプリからの変換された JSON 出力は、次の例のようになります。
  
![出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>その他の Liquid アクションの例
Liquid は、JSON 変換のみに使用されるわけではありません。 Liquid を使用して実行できるその他の変換アクションを以下に示します。

* JSON からテキストへの変換
  
  この例で使用する Liquid テンプレートを次に示します。
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   入力と出力の例を次に示します。
  
   ![JSON からテキストへの出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML から JSON への変換
  
  この例で使用する Liquid テンプレートを次に示します。
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   入力と出力の例を次に示します。

   ![XML から JSON への出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML からテキストへの変換
  
  この例で使用する Liquid テンプレートを次に示します。

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   入力と出力の例を次に示します。

   ![XML からテキストへの出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>次の手順

* [Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  
* [マップについての詳細情報](../logic-apps/logic-apps-enterprise-integration-maps.md "Enterprise Integration のマップについての詳細情報")  

