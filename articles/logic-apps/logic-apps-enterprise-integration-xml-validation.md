---
title: B2B エンタープライズ統合用の XML を検証する
description: Enterprise Integration Pack を備えた Azure Logic Apps でスキーマを使用して XML を検証します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 87650a1ab950f8e88fe08a1c4555c98652776730
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099305"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps でワークフロー用の XML を検証する

企業統合の B2B シナリオでは、多くの場合、契約の取引先は、データ処理を開始する前に、交換するメッセージが有効であることを確認する必要があります。 Azure Logic Apps の **XML Validation** アクションを使用して、定義済みのススキーマに対してドキュメントを検証できます。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* **XML 検証** アクションを使用する空または既存のロジック アプリ ワークフロー。

  空のワークフローがある場合は、任意のトリガーを使用します。 この例では、要求トリガーを使用します。

  ロジック アプリを初めて使用する場合は、次のドキュメントを参照してください。

  * [Azure Logic Apps とは](logic-apps-overview.md)

  * [クイックスタート: 初めてのロジック アプリ ワークフローを作成する](quickstart-create-first-logic-app-workflow.md)

  * [シングル テナントのロジック アプリ ワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

  * [Azure Logic Apps の使用量の測定、課金、各価格モデル](logic-apps-pricing.md)

* **Logic App (従量課金)** リソース タイプを使用する場合は、以下の要件を満たす[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)が必要です。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * **XML 検証** アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * ロジック アプリ リソースに[リンクされている](logic-apps-enterprise-integration-create-integration-account.md#link-account)。

  * XML コンテンツの検証に使用する[スキーマ](logic-apps-enterprise-integration-schemas.md)が含まれている。

  **ロジック アプリ (Standard)** のリソースの種類を使用している場合、統合アカウントにリンクするは必要ありません。 ただし、XML コンテンツの検証に使用する[スキーマ](logic-apps-enterprise-integration-schemas.md)をロジック アプリ リソースに追加する必要があります。 この作業は、ロジック アプリ リソースのメニューにある **[設定]** の **[スキーマ]** セクションで行います。

## <a name="add-xml-validation-action"></a>XML 検証アクションを追加する

1. [Azure portal](https://portal.azure.com) の [デザイナー] ビューで、ロジック アプリとワークフローを開きます。

1. トリガーがない空のロジック アプリがある場合は、必要なトリガーを追加します。 この例では、要求トリガーを使用します。 使用する場合は、次の手順に進みます。

   要求トリガーを追加するには、デザイナーの検索ボックスに `HTTP request` と入力し、**HTTP 要求を受信した場合** という名前の要求トリガーを選択します。

1. ワークフロー中の、**XML 検証** アクションを追加するステップの下で、次のいずれかの手順を選択します。

   従量課金または ISE プランベースのロジック アプリの場合は、次の手順を選択します。

   * ワークフローの最後に **XML 検証** アクションを追加するには、 **[新しいステップ]** を選択します。

   * 既存のステップの間に **XML 検証** アクションを追加するには、それらのステップを接続する矢印にポインターを合わせ、プラス記号 ( **+** ) を表示します。 プラス記号を選択し、 **[アクションの追加]** を選択します。

   Standard プランベースのロジック アプリの場合は、次の手順を選択します。

   * ワークフローの最後に **XML 検証** アクションを追加するには、プラス記号 ( **+** ) を選択し、 **[Add an action (アクションの追加)]** を選択します。

   * 既存のステップの間に **XML 検証** アクションを追加するには、それらのステップの間に表示されるプラス記号 ( **+** ) を選択し、 **[Add an action (アクションの追加)]** を選択します。

1. **[操作を選択してください]** で **[組み込み]** を選択します。 検索ボックスに「 `xml validation`」と入力します。 アクション リストから **[XML の検証]** を選択します。

1. 検証する XML コンテンツを指定するには、 **[コンテンツ]** ボックス内をクリックし、動的コンテンツ リストを表示します。

   動的コンテンツ リストには、ワークフローの前のステップの出力を表すプロパティ トークンが表示されます。 リストに期待するプロパティが表示されない場合は、リストのトリガーやアクションの見出しや、 **[See more (詳細表示)]** を選択できるかどうかを確認します。

   従量課金または ISE プランベースのロジック アプリの場合、デザイナーは次の例のようになります。

   ![マルチテナント デザイナーで動的コンテンツ リストを開いたスクリーンショット。[コンテンツ] ボックスにカーソルを置き、動的コンテンツ リストを開いています。](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   Standard プランベースのロジック アプリの場合、デザイナーは次の例のようになります。

   ![シングルテナント デザイナーで動的コンテンツ リストを開いたスクリーンショット。[コンテンツ] ボックスにカーソルを置き、動的コンテンツ リストを開いています](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. 動的コンテンツ リストから、検証するコンテンツのプロパティ トークンを選択します。

   この例では、トリガーから **[本文]** トークンを選択します。

1. 検証に使用するスキーマを指定するには、**スキーマ名** リストを開き、先に追加したスキーマを選択します。

1. 完了したら、必ずロジック アプリ ワークフローを保存します。

   これで、**XML 検証** アクションの設定が完了しました。 実際のアプリでは、検証したデータを Salesforce などの業務 (LOB) アプリケーションに保存する必要がある場合があります。 検証済みの出力を Salesforce に送信するには、Salesforce アクションを追加します。

1. 検証アクションをテストするには、ワークフローをトリガーして実行します。 たとえば、要求トリガーの場合は、トリガーのエンドポイント URL に要求を送信します。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps で XML 検証のスキーマを追加する](logic-apps-enterprise-integration-schemas.md)
* [Azure Logic Apps でワークフロー用に XML を変換する](logic-apps-enterprise-integration-transform.md)