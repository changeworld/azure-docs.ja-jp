---
title: ワークフローを使用して B2B メッセージを交換する
description: Azure Logic Apps と Enterprise Integration Pack を使用してワークフローを作成することにより、取引先間でメッセージを交換します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/17/2021
ms.openlocfilehash: 6c9580a051a7473e7009cf8df8d2ea8759834683
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351173"
---
# <a name="exchange-b2b-messages-between-partners-using-workflows-in-azure-logic-apps"></a>Azure Logic Apps のワークフローを使用してパートナー間で B2B メッセージを交換する

取引先と契約を定義する統合アカウントがあれば、Azure Logic Apps を使用して取引先間でメッセージを交換する自動化された企業間 (B2B) ワークフローを作成できます。 ワークフローでは、AS2、X12、EDIFACT、RosettaNet などの業界標準のプロトコルをサポートするコネクタを使用できます。 Office 365 Outlook、SQL Server、Salesforce など、[Azure Logic Apps の他のコネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)によって提供される操作を含めることもできます。

この記事では、**要求** トリガーを使用して HTTP 要求を受信し、**AS2 デコード** アクションと **X12 のデコード** アクションを使用してメッセージ コンテンツをデコードし、**応答** アクションを使用して応答を返すことができるロジック アプリ ワークフローのサンプルを作成する方法を説明します。 この例では Azure portal のワークフロー デザイナーを使用しますが、Visual Studio のワークフロー デザイナーでも同様の手順に従うことができます。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、[Azure Logic Apps を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * ロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、ワークフローで成果物を使用するには、統合アカウントに [ロジック アプリ リソースへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)が必要です。

  * [**ロジック アプリ (Standard)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合、統合アカウントにロジック アプリ リソースへのリンクは必要ありませんが、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、または [EDIFACT](logic-apps-enterprise-integration-edifact.md) の操作を使用すると共に、パートナー、契約、証明書などの他の成果物を格納する必要があります。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

  > [!NOTE]
  > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** のリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作が含まれていません。

* 統合アカウント内の少なくとも 2 つの[取引先](logic-apps-enterprise-integration-partners.md)。 両方の取引先の定義では、同じ "*ビジネス ID*" 修飾子 (AS2、X12、EDIFACT、または RosettaNet) を使用する必要があります。

* このワークフローで使用している、パートナー向けの [AS2 契約および X12 契約](logic-apps-enterprise-integration-agreements.md)。 契約には、ホスト パートナーとゲスト パートナーが必要です。

* 空のワークフローを含むロジック アプリ リソース。これに、[要求](../connectors/connectors-native-reqres.md)トリガーを追加した後に、次のアクションを追加できます。

  * [[AS2 デコード]](../logic-apps/logic-apps-enterprise-integration-as2.md) を選択したことを確認します。

  * [条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)。AS2 デコード アクションの成功または失敗に基づいて[要求](../connectors/connectors-native-reqres.md)を送信します。

  * [X12 メッセージのデコード](../logic-apps/logic-apps-enterprise-integration-x12.md)

<a name="add-request-trigger"></a>

## <a name="add-the-request-trigger"></a>要求トリガーを追加する

この例のワークフローを開始するには、要求トリガーを追加します。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) のワークフロー デザイナーで、ロジック アプリ リソースと空のワークフローを開きます。

1. デザイナーの検索ボックスの下の **[すべて]** を選択します (選択されていない場合)。 検索ボックスに「 `when a http request`」と入力します。 **[HTTP 要求の受信時]** という名前の要求トリガーを選択します。

   ![Azure portal とマルチテナント デザイナーを示すスクリーンショット。[HTTP 要求] (検索ボックス内) と、要求トリガーが選択されている。](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-consumption.png)

1. このトリガーの **[要求本文の JSON スキーマ]** ボックスは空のままにしておきます。

   このトリガーでは、フラット ファイル形式で X12 メッセージを受信するためです。

   ![マルチテナント デザイナーと要求トリガーのプロパティを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/request-trigger-consumption.png)

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

   この手順では、後でロジック アプリ ワークフローをトリガーする要求を送信するために使用する **HTTP POST URL** が生成されます。

   ![マルチテナント デザイナーと、要求トリガー用に生成された URL を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-consumption.png)

1. この URL をコピーして、後で使用できるように保存します。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) のワークフロー デザイナーで、ロジック アプリ リソースと空のワークフローを開きます。

1. デザイナーで、 **[操作を選択してください]** を選択します。 検索ボックスの下の **[組み込み]** を選択します (選択されていない場合)。 検索ボックスに「 `when a http request`」と入力します。 **[HTTP 要求の受信時]** という名前の要求トリガーを選択します。

   ![Azure portal とシングルテナント デザイナーを示すスクリーンショット。[HTTP 要求] (検索ボックス内) と、要求トリガーが選択されている。](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-standard.png)

1. このトリガーの **[要求本文の JSON スキーマ]** ボックスは空のままにしておきます。

   このトリガーでは、フラット ファイル形式で X12 メッセージを受信するためです。

   ![シングルテナント デザイナーと要求トリガーのプロパティを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/request-trigger-standard.png)

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

   この手順では、後でロジック アプリ ワークフローをトリガーする要求を送信するために使用する **HTTP POST URL** が生成されます。

   ![シングルテナント デザイナーと、要求トリガー用に生成された URL を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-standard.png)

1. この URL をコピーして、後で使用できるように保存します。

---

<a name="add-decode-as2-trigger"></a>

## <a name="add-the-decode-as2-action"></a>AS2 のデコード アクションを追加する

ここで、この例に必要な B2B アクションを追加します。この例では、AS2 アクションと X12 アクションを使用します。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. トリガーで、 **[新しいステップ]** を選択します。

   > [!TIP]
   > 要求トリガーの詳細を非表示にするには、トリガーのタイトル バーを選択します。

   ![マルチテナント デザイナーと [新しいステップ] が選択されたトリガーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-consumption.png)

1. **[操作を選択してください]** 検索ボックスの下の **[すべて]** を選択します (選択されていない場合)。 検索ボックスに「`as2`」と入力し、 **[AS2 デコード]** を選択します。

   !["AS2 デコード" アクションが選択されているマルチテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-consumption.png)

1. このアクションの **[デコードするメッセージ]** プロパティに、AS2 アクションでデコードする入力を入力します。これは、要求トリガーの `body` 出力です。 このコンテンツをアクションの入力として指定するには複数の方法があります。動的コンテンツ リストから選択する方法と、式として指定する方法です。

   * 使用可能なトリガー出力を示す一覧から選択するには、 **[デコードするメッセージ]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[HTTP 要求の受信時]** で、 **[本文]** プロパティ値を選択します。次に例を示します。

     ![[動的なコンテンツ] リストと [本文] プロパティが選択されたマルチテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-consumption.png)

     > [!TIP]
     > **[HTTP 要求の受信時]** の動的プロパティ リストにトリガー出力がまったく表示されない場合は、 **[もっと見る]** を選択します。

   * トリガーの `body` 出力を参照する式を入力するには、 **[デコードするメッセージ]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[式]** を選択します。 式エディターに次の式を入力して、 **[OK]** を選択します。

     `triggerOutputs()['body']`

     または、 **[デコードするメッセージ]** ボックスに次の式を直接入力します。

     `@triggerBody()`

     この式は、 **[本文]** トークンに解決されます。

     ![解決済みの [本文] プロパティ出力が指定されたマルチテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-consumption.png)

1. アクションの **[メッセージ ヘッダー]** プロパティに、AS2 アクションに必要なすべてのヘッダーを入力します。これらは、要求トリガーの `headers` 出力に含まれるものです。

   1. トリガーの `headers` 出力を参照する式を入力するには、 **[Switch Message headers to text mode]\(メッセージ ヘッダーをテキスト モードに切り替える\)** を選択します。

      ![[メッセージ ヘッダーをテキスト モードに切り替える] が選択されたマルチテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-consumption.png)

   1. **[メッセージ ヘッダー]** ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[式]** を選択します。 式エディターに次の式を入力して、 **[OK]** を選択します。

      `triggerOutputs()['Headers']`

      これで、 **[AS2 デコード]** アクションに、式がトークンとして表示されます。

      ![マルチテナント デザイナーと、"@triggerOutputs()['Headers']" トークンが指定された [メッセージ ヘッダー] ボックスを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-consumption.png)

   1. **Headers** トークンに解決される式トークンを取得するには、デザイナーとコード ビューを切り替えます。 この手順を完了すると、 **[AS2 デコード]** アクションは次の例のように表示されます。

      ![マルチテナント デザイナーと、トリガーからの解決済みヘッダー出力を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. トリガーの下の **[新しいステップの挿入]** (プラス記号) を選択し、 **[アクションの追加]** を選択します。

   ![シングルテナント デザイナーとプラス記号が選択されたトリガーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-standard.png)

1. **[操作を選択してください]** 検索ボックスの下の **[Azure]** を選択します (まだ選択されていない場合)。 検索ボックスに「`as2`」と入力し、 **[AS2 メッセージをデコードする]** を選択します。

   !["AS2 メッセージをデコードする" アクションが選択されたシングルテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-standard.png)

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、接続に使用する名前を指定し、統合アカウントを選択して、 **[作成]** を選択します。

1. このアクションの **[本文]** プロパティに、AS2 アクションでデコードする入力を入力します。これは、要求トリガーの `body` 出力です。 このコンテンツをアクションの入力として指定するには複数の方法があります。動的コンテンツ リストから選択する方法と、式として指定する方法です。

   * 使用可能なトリガー出力を示すリストから選択するには、 **[本文]** プロパティ ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[HTTP 要求の受信時]** で、 **[本文]** プロパティ値を選択します。次に例を示します。

     ![[動的なコンテンツ] リストと [本文] プロパティが選択されたシングルテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-standard.png)

     > [!TIP]
     > **[HTTP 要求の受信時]** の動的プロパティ リストにトリガー出力がまったく表示されない場合は、 **[もっと見る]** を選択します。

   * トリガーの `body` 出力を参照する式を入力するには、 **[本文]** プロパティ ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[式]** を選択します。 式エディターに次の式を入力して、 **[OK]** を選択します。

     `triggerOutputs()['body']`

     または、 **[本文]** プロパティ ボックスに次の式を直接入力します。

     `@triggerBody()`

     この式は、 **[本文]** トークンに解決されます。

     ![解決済みの [本文] プロパティ出力が指定されたシングルテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-standard.png)

1. **[ヘッダー]** プロパティ ボックスに、AS2 アクションに必要なすべてのヘッダーを入力します。これは、要求トリガーの `headers` 出力に含まれるものです。

   1. トリガーの `headers` 出力を参照する式を入力するには、 **[Switch Message headers to text mode]\(メッセージ ヘッダーをテキスト モードに切り替える\)** を選択します。

      ![[メッセージ ヘッダーをテキスト モードに切り替える] が選択されたシングルテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-standard.png)

   1. **[ヘッダー]** プロパティ ボックス内をクリックします。 動的コンテンツ リストが表示されたら、 **[式]** を選択します。 式エディターに次の式を入力して、 **[OK]** を選択します。

      `triggerOutputs()['Headers']`

      これで、 **[AS2 メッセージをデコードする]** アクションに、式がトークンとして表示されます。

      ![シングルテナント デザイナーと、"@triggerOutputs()['Headers']" トークンが指定された [ヘッダー] プロパティを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-standard.png)

   1. **Headers** トークンに解決される式トークンを取得するには、デザイナーとコード ビューを切り替えます。 この手順を完了すると、 **[AS2 デコード]** アクションは次の例のように表示されます。

      ![シングルテナント デザイナーと、トリガーからの解決済みヘッダー出力を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-standard.png)

---

<a name="add-response-action"></a>

## <a name="add-the-response-action-as-a-message-receipt"></a>応答アクションをメッセージ受信として追加する

メッセージを受信したことを取引先に通知するために、条件および応答アクションを使用して、AS2 メッセージ処理通知 (MDN) を含む応答を返すことができます。 AS2 アクションの直後にこれらのアクションを追加することにより、AS2 アクションが成功した場合はロジック アプリ ワークフローで処理を続行できます。 それ以外の場合、AS2 アクションが失敗すると、ロジック アプリ ワークフローの処理は停止します。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. **[AS2 デコード]** アクションで、 **[新しいステップ]** を選択します。

1. **[操作を選択してください]** 検索ボックスの下の **[組み込み]** を選択します (まだ選択されていない場合)。 検索ボックスに「 `condition`」と入力します。 **[条件]** アクションを選択します。

   ![マルチテナント デザイナーと "条件" アクションを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-condition-action-consumption.png)

   条件図形が表示されます。条件が満たされているかどうかを判断するパスも表示されます。

   ![マルチテナント デザイナーと、空のパスを含む条件図形を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/added-condition-action-consumption.png)

1. 次に、評価する条件を指定します。 **[値の選択]** ボックスに、次の式を入力します。

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   中央のボックスの比較演算が `is equal to` に設定されていることを確認します。 右側のボックスに、値 `Expected` を入力します。

1. ロジック アプリ ワークフローを保存します。 式をこのトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

   ![マルチテナント デザイナーと、操作が指定された条件図形を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-consumption.png)

1. 次に、**AS2 デコード** アクションが成功したかどうかに基づいて返す応答を指定します。

   1. **AS2 デコード** アクションが成功した場合について、 **[True]** 図形で **[アクションの追加]** を選択します。 **[操作を選択してください]** 検索ボックスの下に、「`response`」と入力して、 **[応答]** を選択します。

      ![マルチテナント デザイナーと "応答" アクションを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/select-response-consumption.png)

   1. **AS2 デコード** アクションの出力から AS2 MDN にアクセスするには、次の式を指定します。

      * **[応答]** アクションの **[ヘッダー]** プロパティに、次の式を入力します。

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * **[応答]** アクションの **[本文]** プロパティに、次の式を入力します。

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 式をトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

      ![マルチテナント デザイナーと、AS2 MDN にアクセスするための解決済みの式を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-consumption.png)

   1. **AS2 デコード** アクションが失敗した場合について、 **[False]** 図形で、 **[アクションの追加]** を選択します。 **[操作を選択してください]** 検索ボックスの下に、「`response`」と入力して、 **[応答]** を選択します。 必要な状態とエラーを返すように **応答** アクションを設定します。

1. ロジック アプリ ワークフローを保存します。

### <a name="standard"></a>[Standard](#tab/standard)

1. **[AS2 メッセージをデコードする]** アクションの下の **[新しいステップの挿入]** (プラス記号) を選択し、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** 検索ボックスの下の **[組み込み]** を選択します (まだ選択されていない場合)。 検索ボックスに「 `condition`」と入力します。 **[条件]** アクションを選択します。

   !["条件" アクションが選択されたシングルテナント デザイナーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-condition-action-standard.png)

   条件図形が表示されます。条件が満たされているかどうかを判断するパスも表示されます。

   ![シングルテナント デザイナーと、空のパスを含む "条件" 図形を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/added-condition-action-standard.png)

1. 次に、評価する条件を指定します。 詳細パネルを表示するため、 **[条件]** 図形を選択します。 **[値の選択]** ボックスに、次の式を入力します。

   `@body('Decode_AS2_message')?['AS2Message']?['MdnExpected']`

   中央のボックスの比較演算が `is equal to` に設定されていることを確認します。 右側のボックスに、値 `Expected` を入力します。

1. ロジック アプリ ワークフローを保存します。 式をこのトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

   ![シングルテナント デザイナーと、操作が指定された条件図形を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-standard.png)

1. 次に、 **[AS2 メッセージをデコードする]** アクションが成功したかどうかに基づいて返す応答を指定します。

   1. **[AS2 メッセージをデコードする]** アクションが成功した場合について、 **[True]** 図形でプラス記号を選択し、 **[アクションの追加]** を選択します。 **[アクションの追加]** ペインの **[操作を選択してください]** 検索ボックスに、「`response`」と入力して、 **[応答]** を選択します。

      ![シングルテナント デザイナーと "応答" アクションを示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/select-response-standard.png)

   1. **AS2 デコード** アクションの出力から AS2 MDN にアクセスするには、次の式を指定します。

      * **[応答]** アクションの **[ヘッダー]** プロパティに、次の式を入力します。

        `@body('Decode_AS2_message')?['OutgoingMdn']?['OutboundHeaders']`

      * **[応答]** アクションの **[本文]** プロパティに、次の式を入力します。

        `@body('Decode_AS2_message')?['OutgoingMdn']?['Content']`

   1. 式をトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

      ![シングルテナント デザイナーと、AS2 MDN にアクセスするための解決済みの式を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-standard.png)

   1. **[AS2 メッセージをデコードする]** アクションが失敗した場合について、 **[False]** 図形でプラス記号を選択し、 **[アクションの追加]** を選択します。 **[アクションの追加]** ペインの **[操作を選択してください]** 検索ボックスに、「`response`」と入力して、 **[応答]** を選択します。 必要な状態とエラーを返すように **応答** アクションを設定します。

1. ロジック アプリ ワークフローを保存します。

---

<a name="add-decode-x12-action"></a>

## <a name="add-the-decode-x12-message-action"></a>[X12 メッセージをデコードする] アクションを追加する

次に、**X12 メッセージのデコード** アクションを追加します。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. **[応答]** アクションで、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** の下の検索ボックスに「`x12 decode`」と入力して、 **[X12 メッセージをデコードする]** を選択します。

   ![マルチテナント デザイナーと、"X12 メッセージをデコードする" アクションが選択された状態を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-consumption.png)

1. X12 アクションで接続情報の入力を求められたら、接続の名前を指定して、使用する統合アカウントを選択した後、 **[作成]** を選択します。

   ![マルチテナント デザイナーと統合アカウントへの接続を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-consumption.png)

1. 次に、X12 アクションの入力を指定します。 この例では、AS2 アクションからの出力を使用します。これは、メッセージのコンテンツですが、このコンテンツは JSON オブジェクト形式で、Base64 でエンコードされることに注意してください。 このため、このコンテンツを文字列に変換する必要があります。

   **[デコードする X12 フラット ファイル メッセージ]** ボックスに、AS2 出力を変換する次の式を入力します。

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

1. ロジック アプリ ワークフローを保存します。 式をこのトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

    ![マルチテナント デザイナーと、Base64 でエンコードされたコンテンツから文字列への変換を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-consumption.png)

1. ロジック アプリ ワークフローを保存します。

   このロジック アプリ ワークフローに追加のステップが必要な場合 (メッセージ コンテンツをデコードして、そのコンテンツを JSON オブジェクト形式で出力する場合など) は、必要なアクションをロジック アプリ ワークフローに引き続き追加します。

### <a name="standard"></a>[Standard](#tab/standard)

1. **[応答]** アクションの下のプラス記号を選択して、 **[アクションの追加]** を選択します。 **[アクションの追加]** ペインの **[操作を選択してください]** 検索ボックスの下の **[Azure]** を選択します (まだ選択されていない場合)。 検索ボックスに「`x12 decode`」と入力し、 **[X12 メッセージをデコードする]** を選択します。

   ![シングルテナント デザイナーと、"X12 メッセージをデコードする" アクションが選択された状態を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-standard.png)

1. X12 アクションで接続情報の入力を求められたら、接続の名前を指定して、使用する統合アカウントを選択した後、 **[作成]** を選択します。

   ![シングルテナント デザイナーと統合アカウントへの接続を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-standard.png)

1. 次に、X12 アクションの入力を指定します。 この例では、AS2 アクションからの出力を使用します。これは、メッセージのコンテンツですが、このコンテンツは JSON オブジェクト形式で、Base64 でエンコードされることにご注意ください。 このため、このコンテンツを文字列に変換する必要があります。

   **[デコードする X12 フラット ファイル メッセージ]** ボックスに、AS2 出力を変換する次の式を入力します。

   `@base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])`

1. ロジック アプリ ワークフローを保存します。 式をこのトークンとして解決するには、[デザイナー] と [コード ビュー] を切り替えます。

    ![シングルテナント デザイナーと、Base64 でエンコードされたコンテンツから文字列への変換を示すスクリーンショット。](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-standard.png)

1. ロジック アプリ ワークフローをもう一度保存します。

   このロジック アプリ ワークフローに追加のステップが必要な場合 (メッセージ コンテンツをデコードして、そのコンテンツを JSON オブジェクト形式で出力する場合など) は、必要なアクションをロジック アプリ ワークフローに引き続き追加します。

---

これで、B2B ロジック アプリ ワークフローの設定が完了しました。 実際のアプリでは、デコードした X12 データを基幹業務 (LOB) アプリやデータ ストアに保存できます。 例として、次のドキュメントを参照してください。

* [Azure Logic Apps から SAP システムに接続する](logic-apps-using-sap-connector.md)

* [SSH と Azure Logic Apps を使用して SFTP ファイルの監視、作成、および管理を行う](../connectors/connectors-sftp-ssh.md)

独自の LOB アプリに接続してロジック アプリでこれらの API を使用するために、さらにアクションを追加したり、[カスタム API を作成](logic-apps-create-api-app.md)したりすることができます。

## <a name="next-steps"></a>次のステップ

* [HTTPS 呼び出しを受信して応答する](../connectors/connectors-native-reqres.md)
* [B2B エンタープライズ統合用の AS2 メッセージを交換する](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [B2B エンタープライズ統合用の X12 メッセージを交換する](../logic-apps/logic-apps-enterprise-integration-x12.md)
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) について詳細を確認する
