---
title: B2B エンタープライズ統合用に XML を変換する
description: Enterprise Integration Pack を備えた Azure Logic Apps でマップを使用して XML を変換します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 30895da003122b760d6437b3cd14a270482f7a0a
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105260"
---
# <a name="transform-xml-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps でワークフロー用に XML を変換する

エンタープライズ統合の企業間 (B2B) シナリオでは、XML を形式間で変換する必要がある場合があります。 Azure Logic Apps では、ロジック アプリ ワークフローで、XSLT (Extensible Stylesheet Language Transformation) マップと **XML 変換** アクションを使用して XML を変換できます。 マップとは、データを XML から別の形式に変換する方法について記述した XML ドキュメントのことです。 このドキュメントは、入力のソース XML スキーマと出力のターゲット XML スキーマで構成されています。  さまざまな組み込み関数を使用すると、文字列操作、条件付き割り当て、算術式、日時の書式設定や、ループ構造の操作などで、データを操作および制御できます。

たとえば、YearMonthDay という日付形式 (YYYYMMDD) を使用している顧客から、B2B の注文や請求書を定期的に受け取っているとします。 ただし、あなたの組織では MonthDayYear という日付形式 (MMDDYYYY) を使用しています。 そのような場合は、YearMonthDay 形式から MonthDayYear 形式に変換するマップを作成して使用できます。その後、顧客活動データベースに注文や請求書の詳細を保存できます。

[マップを作成](logic-apps-enterprise-integration-maps.md#create-maps)してマップが機能することを確認したら、マルチテナントの従量課金プランベースのロジック アプリまたは ISE プランベースのロジック アプリにリンクされている統合アカウントにこのマップを追加するか、シングルテナントの Standard プランベースのロジック アプリにマップを直接追加できます。 詳細については、[Azure Logic Apps での XML 変換用の XSLT マップの追加](logic-apps-enterprise-integration-maps.md)に関する記事を参照してください。 ワークフローに **XML 変換** アクションが含まれていると仮定すると、ワークフローがトリガーされて XML コンテンツが変換に使用できる場合に、そのアクションが実行されます。

ロジック アプリを初めて使用する場合は、次のドキュメントを参照してください。

* [Azure Logic Apps とは - リソースの種類とホスト環境](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [シングルテナントの Azure Logic Apps (Standard) で統合ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

* [シングルテナントのロジック アプリのワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

* [Azure Logic Apps の使用量の測定、課金、価格モデル](logic-apps-pricing.md)

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* ワークフローで必要に応じて **XML 変換** アクションを追加できるように、既にトリガーで開始されているロジック アプリ ワークフロー。

* **ロジック アプリ (Standard)** のリソースの種類を使用している場合、統合アカウントは必要ありません。 代わりに、Azure portal または Visual Studio Code でロジック アプリ リソースにマップを直接追加できます。 現在は XSLT 1.0 のみがサポートされています。 その後、"*同じロジック アプリ リソース*" 内の複数のワークフローでこれらのマップを使用できます。

* **ロジック アプリ (従量課金)** のリソースの種類を使用している場合は、エンタープライズ統合および企業間 (B2B) ソリューションで使用するマップや他の成果物を格納できる[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)が必要です。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * **XML 変換** アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * **XML 変換** アクションを使用するロジック アプリ リソースに[リンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)されている。

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