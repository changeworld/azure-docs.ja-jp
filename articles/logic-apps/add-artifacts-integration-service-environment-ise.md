---
title: 成果物を統合サービス環境に追加する
description: 統合サービス環境 (ISE) にロジック アプリ、統合アカウント、カスタム コネクタを追加して Azure 仮想ネットワーク (VNET) にアクセスする
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: c597bc4430e4390f0e29e4fe8ae4014521e1ae74
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732251"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps で統合サービス環境 (ISE) に成果物を追加する

[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成したら、ロジック アプリ、統合アカウント、コネクタなどの成果物を追加して、Azure 仮想ネットワーク内のリソースにアクセスできるようにします。 たとえば、ISE の作成後に使用可能になる ISE マネージド コネクタは、ロジック アプリ デザイナーに自動的には表示されません。 これらの ISE コネクタを使用するには、手動で[これらのコネクタを追加して ISE にデプロイ](#add-ise-connectors-environment)し、ロジック アプリ デザイナーに表示されるようにする必要があります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ロジック アプリを実行するために作成した ISE。 ISE がない場合は、[まず ISE を作成します](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>ロジック アプリを作成する

統合サービス環境 (ISE) で実行されるロジック アプリを構築するには、次の手順を実行します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[ロジック アプリ]**  >  **[追加]** の順に選択します。

   ![ISE に新しいロジック アプリを追加する](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   または

   Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

1. ロジック アプリに使用する名前、Azure サブスクリプション、および Azure リソース グループ (新規または既存) を入力します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧で、ご使用の ISE を選択します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > ロジック アプリを統合アカウントで使用する場合は、それらのロジック アプリと統合アカウントで同じ ISE を使用する必要があります。

1. 引き続き[通常の方法でロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

   ISE を使用するときに、グローバル Logic Apps サービスと比較して、トリガーとアクションがどのように機能するか、およびそれらがどのようにラベル付けされるかの違いについては、[ISE での分離とグローバルの概要](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)に関するページを参照してください。

1. ISE でロジック アプリと API 接続を管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>統合アカウントを作成する

作成時に選択された [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に基づいて、ISE には追加費用なしで特定の統合アカウントの使用が含まれます。 統合サービス環境 (ISE) に存在するロジック アプリは、同じ ISE に存在する統合アカウントのみを参照できます。 そのため、ISE で統合アカウントがロジック アプリと連携するには、統合アカウントとロジック アプリの両方で、場所として "*同じ環境*" を使用する必要があります。 統合アカウントと ISE の詳細については、「[ISE との統合アカウント](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)」を参照してください。

ISE を使用する統合アカウントを作成するには、次の手順を実行します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[統合アカウント]**  >  **[追加]** の順に選択します。

   ![ISE に新しい統合アカウントを追加する](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   または

   Azure のメイン メニューから、 **[リソースの作成]**  >  **[統合]**  >  **[統合アカウント]** の順に選択します。

1. 統合アカウントに使用する名前、Azure サブスクリプション、Azure リソース グループ (新規または既存)、および価格レベルを指定します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧から、ロジック アプリで使用しているものと同じ ISE を選択します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [通常の方法でロジック アプリを統合アカウントにリンクします](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. [取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)や[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)などの成果物を統合アカウントに追加して続行します。

1. ISE で統合アカウントを管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE のコネクタを追加する

ISE の作成後に使用可能になる Microsoft が管理するコネクタは、ロジック アプリ デザイナーのコネクタ ピッカーに自動的には表示されません。 これらの ISE コネクタを使用するには、手動でこれらのコネクタを追加して ISE にデプロイし、ロジック アプリ デザイナーに表示されるようにする必要があります。

1. ISE メニューの **[設定]** で、 **[マネージド コネクタ]** を選択します。 ツールバーの **[追加]** を選択します。

   ![マネージド コネクタを表示する](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **[新しいマネージド コネクタの追加]** ウィンドウの **[コネクタの検索]** ボックスの一覧を開きます。 使用したいが、まだ ISE にデプロイされていない ISE コネクタを選択します。 **作成** を選択します。

   ![ISE にデプロイする ISE コネクタを選択します](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   選択できるのは、適格であっても、まだ ISE にデプロイされていない ISE コネクタだけです。 自分の ISE に既にデプロイされているコネクタは選択不可の状態で表示されます。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>カスタム コネクタを作成する

ISE でカスタム コネクタを使用するには、ISE 内から直接これらのカスタム コネクタを作成します。

1. ISE をまだ開いていない場合は、見つけて開きます。 ISE メニューの **[設定]** で、 **[カスタム コネクタ]**  >  **[追加]** の順に選択します。

   ![カスタム コネクタの作成](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. カスタム コネクタに使用する名前、Azure サブスクリプション、および Azure リソース グループ (新規または既存) を入力します。

1. **[統合サービス環境]** セクションの **[場所]** 一覧から、ロジック アプリで使用しているものと同じ ISE を選択し、 **[作成]** を選択します。次に例を示します。

   ![統合サービス環境を選択する](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. 新しいカスタム コネクタを選択し、 **[編集]** を選択します。次に例を示します。

   ![カスタム コネクタを選択して編集する](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [OpenAPI 定義](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)または [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector) から通常の方法でコネクタを作成して続行します。

1. ISE でカスタム コネクタを管理するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [統合サービス環境を管理する](../logic-apps/ise-manage-integration-service-environment.md)
