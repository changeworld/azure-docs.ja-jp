---
title: エンタープライズ統合ワークフローで XML を検証する
description: Azure Logic Apps と Enterprise Integration Pack を使用し、ワークフローでスキーマを使用して XML を検証します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 20edb5f3b94e9014ce08d012cb0033a3bfb28db5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457470"
---
# <a name="validate-xml-in-workflows-with-azure-logic-apps"></a>Azure Logic Apps を使用してワークフローで XML を検証する

企業統合の B2B シナリオでは、多くの場合、契約の取引先は、データ処理を開始する前に、交換するメッセージが有効であることを確認する必要があります。 ロジック アプリ ワークフローでは、**XML 検証** アクションと定義済みの [スキーマ](logic-apps-enterprise-integration-schemas.md)を使用して、XML メッセージとドキュメントを検証することができます。

ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、「[Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)」をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* **XML 検証** アクションを使用する空または既存のロジック アプリ ワークフロー。

  空のワークフローがある場合は、任意のトリガーを使用します。 この例では、要求トリガーを使用します。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * **XML 検証*** アクションを使用する予定のロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**ロジック アプリ (従量課金)** リソース タイプ](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用する場合、統合アカウントには以下のアイテムが必要です。

    * XML コンテンツの検証に使用する[スキーマ](logic-apps-enterprise-integration-schemas.md)。

    * [ご利用のロジック アプリ リソースへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)。

  * [**ロジック アプリ (Standard)** リソース タイプ](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合は、統合アカウントにスキーマを保存しません。 代わりに、Azure portal または Visual Studio Code を使用して[ロジック アプリ リソースにスキーマを直接追加](logic-apps-enterprise-integration-schemas.md)することができます。 その後、"*同じロジック アプリ リソース*" 内の複数のワークフローでこれらのスキーマを使用できます。

    取引先、契約、証明書などの他の成果物を保存すると共に、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、および [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作を使用するには、引き続き統合アカウントが必要です。 ただし、ロジック アプリ リソースを統合アカウントにリンクする必要はないため、リンク機能は存在しません。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

    > [!NOTE]
    > 現時点では、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) の操作をサポートしているのは、**ロジック アプリ (従量課金)** のリソースの種類のみです。 **ロジック アプリ (Standard)** のリソースの種類には、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作は含まれていません。

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

   動的コンテンツ リストには、ワークフローの前のステップの出力を表すプロパティ トークンが表示されます。 リストに想定されるプロパティが表示されない場合は、リストのトリガーまたはアクションの見出しと、 **[詳細表示]** を選択できるかどうかを確認します。

   従量課金または ISE プランベースのロジック アプリの場合、デザイナーは次の例のようになります。

   ![マルチテナント デザイナーを示すスクリーンショット。動的コンテンツ リストが開いて、[コンテンツ] ボックスにカーソルが置かれています。](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   Standard プランベースのロジック アプリの場合、デザイナーは次の例のようになります。

   ![シングルテナント デザイナーを示すスクリーンショット。動的コンテンツ リストが開いて、[コンテンツ] ボックスにカーソルが置かれています](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. 動的コンテンツ リストから、検証するコンテンツのプロパティ トークンを選択します。

   この例では、トリガーから **[本文]** トークンを選択します。

1. 検証に使用するスキーマを指定するには、**スキーマ名** リストを開き、先に追加したスキーマを選択します。

1. 完了したら、必ずロジック アプリ ワークフローを保存します。

   これで、**XML 検証** アクションの設定が完了しました。 実際のアプリでは、検証したデータを Salesforce などの業務 (LOB) アプリケーションに保存する必要がある場合があります。 検証済みの出力を Salesforce に送信するには、Salesforce アクションを追加します。

1. 検証アクションをテストするには、ワークフローをトリガーして実行します。 たとえば、要求トリガーの場合は、トリガーのエンドポイント URL に要求を送信します。

   **XML 検証** アクションは、ワークフローがトリガーされた後に、XML コンテンツが検証に使用できるようになると実行されます。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps で XML 検証のスキーマを追加する](logic-apps-enterprise-integration-schemas.md)
* [Azure Logic Apps でワークフロー用に XML を変換する](logic-apps-enterprise-integration-transform.md)
