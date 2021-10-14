---
title: エンタープライズ統合ワークフローで XML を変換する
description: Enterprise Integration Pack を備えた Azure Logic Apps でマップを使用して XML を変換します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 84d0e0b509505e6d56e39294b3e2819e4b03e024
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350001"
---
# <a name="transform-xml-in-workflows-with-azure-logic-apps"></a>Azure Logic Apps を使用してワークフローで XML を変換する

エンタープライズ統合の企業間 (B2B) シナリオでは、XML を形式間で変換する必要がある場合があります。 ロジック アプリ ワークフローでは、**XML 変換** アクションと定義済みの [ "*マップ*"](logic-apps-enterprise-integration-maps.md)を使用して、XML を変換することができます。 たとえば、YearMonthDay という日付形式 (YYYYMMDD) を使用している顧客から、B2B の注文や請求書を定期的に受け取っているとします。 ただし、あなたの組織では MonthDayYear という日付形式 (MMDDYYYY) を使用しています。 そのような場合は、YearMonthDay 形式から MonthDayYear 形式に変換するマップを作成して使用できます。その後、顧客活動データベースに注文や請求書の詳細を保存できます。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、「[Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)」をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* ワークフローで必要に応じて **XML 変換** アクションを追加できるように、既にトリガーで開始されているロジック アプリ ワークフロー。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * **XML 変換** アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** リソース タイプ](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用する場合、統合アカウントには以下のアイテムが必要です。

    * XML コンテンツの変換に使用する[マップ](logic-apps-enterprise-integration-maps.md)。

    * [ご利用のロジック アプリ リソースへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)。

  * [**ロジック アプリ (Standard)** というリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合は、統合アカウントにマップを保存しません。 代わりに、Azure portal または Visual Studio Code を使用して、[ロジック アプリ リソースにマップを直接追加](logic-apps-enterprise-integration-maps.md)することができます。 現在は XSLT 1.0 のみがサポートされています。 その後、"*同じロジック アプリ リソース*" 内の複数のワークフローでこれらのマップを使用できます。

    取引先、契約、証明書などの他の成果物を保存すると共に、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、および [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作を使用するには、引き続き統合アカウントが必要です。 ただし、ロジック アプリ リソースを統合アカウントにリンクする必要はないため、リンク機能は存在しません。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

    > [!NOTE]
    > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** というリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作は含まれていません。

## <a name="add-transform-xml-action"></a>XML 変換アクションを追加する

1. [Azure portal](https://portal.azure.com) のデザイナー ビューで、ロジック アプリとワークフローを開きます。

1. トリガーがない空のロジック アプリがある場合は、必要なトリガーを追加します。 この例では、要求トリガーを使用します。 使用する場合は、次の手順に進みます。

   要求トリガーを追加するには、デザイナーの検索ボックスに「`HTTP request`」と入力し、 **[HTTP 要求を受信した場合]** という名前の要求トリガーを選択します。

1. **XML 変換** アクションを追加するワークフローの手順で、次のいずれかの手順を選択します。

   従量課金または ISE プランベースのロジック アプリの場合は、次の手順から選択します。

   * ワークフローの最後に **XML 変換** アクションを追加するには、 **[新しいステップ]** を選択します。

   * 既存のステップの間に **XML 変換** アクションを追加するには、それらのステップを接続する矢印にポインターを合わせて、プラス記号 ( **+** ) を表示します。 プラス記号を選択し、 **[アクションの追加]** を選択します。

   Standard プランベースのロジック アプリの場合は、次の手順から選択します。

   * ワークフローの最後に **XML 変換** アクションを追加するには、プラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

   * 既存のステップの間に **XML 変換** アクションを追加するには、それらのステップの間に表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** で **[組み込み]** を選択します。 検索ボックスに「 `transform xml`」と入力します。 アクションの一覧から、 **[XML 変換]** を選択します。

1. 変換の XML コンテンツを指定するには、HTTP 要求で受け取る任意の XML データを使用できます。 **[コンテンツ]** ボックス内をクリックして、動的コンテンツ リストを表示します。

   動的コンテンツ リストには、ワークフローの前のステップの出力を表すプロパティ トークンが表示されます。 リストに想定されるプロパティが表示されない場合は、リストのトリガーまたはアクションの見出しと、 **[詳細表示]** を選択できるかどうかを確認します。

   従量課金または ISE プランベースのロジック アプリの場合、デザイナーは次の例のようになります。

   ![マルチテナント デザイナーを示すスクリーンショット。動的コンテンツ リストが開いて、[コンテンツ] ボックスにカーソルが置かれています。](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-multi-tenant.png)

   Standard プランベースのロジック アプリの場合、デザイナーは次の例のようになります。

   ![シングルテナント デザイナーを示すスクリーンショット。動的コンテンツ リストが開いて、[コンテンツ] ボックスにカーソルが置かれています](./media/logic-apps-enterprise-integration-transform/open-dynamic-content-list-single-tenant.png)

1. 動的コンテンツ リストから、検証するコンテンツのプロパティ トークンを選択します。

   この例では、トリガーから **[本文]** トークンを選択します。

   > [!NOTE]
   > 選択するコンテンツが XML であることを確認してください。 コンテンツが XML ではないか、Base64 でエンコードされている場合は、コンテンツを処理する式を指定する必要があります。 たとえば、コンテンツをデコードする `base64ToBinary()`、コンテンツを XML として処理する `xml()` などの[式の関数](workflow-definition-language-functions-reference.md)を使用できます。

1. 変換に使用するマップを指定するには、**マップ** の一覧を開いて、前に追加したマップを選択します。

1. 完了したら、必ずロジック アプリ ワークフローを保存してください。

   これで、**XML 変換** アクションの設定が完了しました。 実際のアプリでは、変換したデータを Salesforce などの基幹業務 (LOB) アプリに保存することが必要になる場合があります。 変換された出力を Salesforce に送信するには、Salesforce アクションを追加します。

1. 変換アクションをテストするには、ワークフローをトリガーして実行します。 たとえば、要求トリガーの場合は、トリガーのエンドポイント URL に要求を送信します。

   **XML 変換** アクションは、ワークフローがトリガーされた後に、XML コンテンツが変換に使用できるようになると実行されます。

## <a name="advanced-capabilities"></a>拡張機能

### <a name="reference-assembly-or-custom-code-from-maps"></a>マップからのアセンブリまたはカスタム コードの参照

**ロジック アプリ (従量課金)** ワークフローでは、**XML 変換** アクションで、外部アセンブリを参照するマップがサポートされます。 詳細については、[Azure Logic Apps でのワークフローのための XSLT マップの追加](logic-apps-enterprise-integration-maps.md#add-assembly)に関する記事を参照してください。

### <a name="byte-order-mark"></a>バイト順マーク (BOM: Byte Order Mark)

既定では、変換からの応答はバイト オーダー マーク (BOM) から始まります。 この機能にアクセスできるのは、コード ビュー エディターで作業しているときのみです。 この機能を無効にするには、`transformOptions` プロパティを `disableByteOrderMark` に設定します。

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps で XML 変換用の XSLT マップを追加する](logic-apps-enterprise-integration-maps.md)
* [Azure Logic Apps でワークフロー用の XML を検証する](logic-apps-enterprise-integration-xml-validation.md)
