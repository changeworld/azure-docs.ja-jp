---
title: 統合アカウントを作成および管理する
description: Enterprise Integration Pack を使用して Azure Logic Apps で B2B エンタープライズ統合ワークフローを構築するための統合アカウントを作成および管理します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: c140c8495b1179298d6ca0029f430896aab55f7f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360846"
---
# <a name="create-and-manage-integration-accounts-for-b2b-workflows-in-azure-logic-apps-with-the-enterprise-integration-pack"></a>Enterprise Integration Pack で、Azure Logic Apps の B2B ワークフローを使用するための統合アカウントを作成、管理する

Azure Logic Apps を使用して企業間 (B2B) およびエンタープライズ統合ワークフローを構築する前に、"*統合アカウント*" リソースを作成する必要があります。 このアカウントは、Azure のスケーラブルなクラウドベースのコンテナーであり、B2B シナリオのワークフローで定義して使用する B2B 成果物を簡単に格納および管理することができます。 このような成果物には、[取引先](logic-apps-enterprise-integration-partners.md)、[契約](logic-apps-enterprise-integration-agreements.md)、[マップ](logic-apps-enterprise-integration-maps.md)、[スキーマ](logic-apps-enterprise-integration-schemas.md)、[証明書](logic-apps-enterprise-integration-certificates.md)などが含まれます。 また、B2B メッセージを他の組織と電子的に交換するには、統合アカウントが必要です。 他の組織でご自身の組織とは異なるプロトコルとメッセージ形式が使用されている場合、ご自身の組織のシステムでそれらのメッセージを処理できるように、形式を変換する必要があります。 サポートされている業界標準プロトコルとして、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](logic-apps-enterprise-integration-edifact.md)、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) があります。

> [!TIP]
> [統合サービス環境](connect-virtual-network-vnet-isolated-environment-overview.md)で使用するための統合アカウントを作成するには、[ISE での統合アカウント作成](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)に関する記事をご確認ください。

この記事では、次のタスクを完了する方法について説明します。

* 統合アカウントを作成する。
* ロジック アプリ リソースに統合アカウントをリンクする。
* 統合アカウントの価格レベルを変更する。
* ロジック アプリから統合アカウントのリンクを解除する。
* 別の Azure リソース グループまたはサブスクリプションに統合アカウントを移動する。
* 統合アカウントを削除する。

Azure Logic Apps を初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」を参照してください。 B2B エンタープライズ統合の詳細については、「[Azure Logic Apps と Enterprise Integration Pack を使用した B2B エンタープライズ統合ワークフロー](logic-apps-enterprise-integration-overview.md)」をご確認ください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。 統合アカウントとロジック アプリ リソースの両方に同じ Azure サブスクリプションを使用する必要があります。

* [**ロジック アプリ (従量課金)** のリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合は、[統合アカウントにリンク](#link-account)できるロジック アプリ リソースが必要です。 このリンクは、ワークフローで成果物を使用するために必要です。 このリンクを使用せずに成果物を作成することもできますが、ワークフローでこれらの成果物を使用する準備ができた時点で、リンクが必要になります。

* [**ロジック アプリ (Standard)** というリソースの種類](logic-apps-overview.md#resource-type-and-host-environment-differences)を使用している場合は、Azure portal または Visual Studio Code を使用して、ロジック アプリ リソースにマップとスキーマを直接追加できます。 その後、"*同じロジック アプリ リソース*" 内の複数のワークフローで、これらの成果物を使用できます。 その場合でも、他の B2B 成果物用の統合アカウントを作成し、[AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md)、[EDIFACT](logic-apps-enterprise-integration-edifact.md)、[RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作などの B2B 操作を使用する必要があります。 ただし、統合アカウントをロジック アプリ リソースにリンクする必要はないため、リンク機能は存在しません。

## <a name="create-integration-account"></a>統合アカウントを作成する

統合アカウントは、[価格が異なる](https://azure.microsoft.com/pricing/details/logic-apps/)さまざまなレベルで使用できます。 統合アカウントを作成すると、選択したレベルに基づいてコストが発生する場合があります。 詳しくは、[Logic Apps の価格と課金モデル](logic-apps-pricing.md#integration-accounts)に関する記事、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関するページをご確認ください。

要件とシナリオに基づいて、作成する適切な統合アカウント レベルを決定します。 統合アカウントとロジック アプリ リソースの両方に、"*同じ*" 場所または Azure リージョンを使用する必要があります。 次の表に、使用できるレベルを示します。

| レベル | 説明 |
|------|-------------|
| **Basic** | メッセージの処理のみを必要とするシナリオ、または大規模なビジネス エンティティと取引パートナー関係がある小規模なビジネス パートナーとして機能する場合。 <p><p>Logic Apps の SLA でサポートされています。 |
| **Standard** | より複雑な B2B 関係があり、管理する必要があるエンティティの数が増えているシナリオ。 <p><p>Logic Apps の SLA でサポートされています。 |
| **Free** | 調査シナリオ用であり、運用シナリオでは使用しないでください。 このレベルには、リージョンの可用性、スループット、使用量に関する制限があります。 たとえば、Free レベルは、米国西部や東南アジアなどの Azure のパブリック リージョンのみで利用できますが、[Azure China 21Vianet](/azure/china/overview-operations) または [Azure Government](../azure-government/documentation-government-welcome.md) で利用することはできません。 <p><p>**注**:Logic Apps の SLA でサポートされていません。 |
|||

このタスクの場合、Azure portal、[Azure CLI](/cli/azure/resource#az_resource_create)、または [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount) を使用できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

1. **[統合アカウント]** で **[作成]** を選択します。

1. **[統合アカウントの作成]** ペインで、統合アカウントに関する次の情報を指定します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | Azure サブスクリプションの名前 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 関連するリソースを整理するために使用する [Azure リソース グループ](../azure-resource-manager/management/overview.md)の名前。 この例では、`FabrikamIntegration-RG` という名前の新しいリソース グループを作成します。 |
   | **統合アカウント名** | はい | <*integration-account-name*> | 統合アカウントの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、`Fabrikam-Integration` を使用します。 |
   | **リージョン** | はい | <*Azure-region*> | 統合アカウント メタデータの保存先となる Azure リージョン。 ロジック アプリと同じ場所を選択するか、統合アカウントと同じ場所でロジック アプリを作成します。 この例では、`West US` を使用します。 <p>**注**: [統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) 内で統合アカウントを作成するには、 **[統合サービス環境に関連付ける]** を選択し、その ISE を場所として選択します。 詳細については、「[ISE で統合アカウントを作成する](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)」を参照してください。 |
   | **価格レベル** | はい | <*pricing-level*> | 統合アカウントの価格レベル。後で変更できます。 この例では **[Free]** を選択します。 詳細については、次のドキュメントを確認してください。 <p>- [Logic Apps の価格モデル](logic-apps-pricing.md#integration-accounts) <br>- [Logic Apps の制限と構成](logic-apps-limits-and-config.md#integration-account-limits) <br>- [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Log Analytics を有効にする** | いいえ | 未選択 | この例では、このオプションを選択しないでください。 |
   |||||

1. 終わったら、**[確認と作成]** を選択します。

   デプロイが完了すると、Azure によって統合アカウントが開かれます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. [az logic integration-account](/cli/azure/logic/integration-account) 拡張機能を追加するには、[az extension add](/cli/azure/extension#az_extension_add)コマンドを使用します。

   ```azurecli
   az extension add –-name logic
   ```

1. リソース グループを作成するか、既存のリソース グループを使用するには、[az group create](/cli/azure/group#az_group_create) コマンドを実行します。

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   リソース グループの統合アカウントの一覧を表示するには、[az logic integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list) コマンドを使用します。

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. 統合アカウントを作成するには、[az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) コマンドを実行します。

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   統合アカウントの名前には、文字、数字、ハイフン (-)、アンダースコア (_)、かっこ ((、))、およびピリオド (.) のみを含めることができます。

   特定の統合アカウントを表示するには、[az logic integration-account show](/cli/azure/logic/integration-account#az_logic_integration_account_show) コマンドを使用します。

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update) コマンドを使用すると、SKU または価格レベルを変更できます。

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   価格の詳細については、こちらのリソースを参照してください。

   * [Logic Apps の価格モデル](logic-apps-pricing.md#integration-accounts)
   * [Logic Apps の制限と構成](logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)

JSON ファイルを使用して統合アカウントをインポートするには、[az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import) コマンドを使用します。

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

---

<a name="link-account"></a>

## <a name="link-to-logic-app-consumption-resource-only"></a>ロジック アプリへのリンク (従量課金リソースのみ)

**ロジック アプリ (従量課金)** ワークフローで統合アカウントの B2B 成果物にアクセスするには、事前にロジック アプリ リソースを統合アカウントにリンクしておく必要があります。 ロジック アプリと統合アカウントの両方に同じ Azure サブスクリプションと Azure リージョンを使用する必要があります。 このタスクを完了するには、Azure portal を使用します。 Visual Studio を使用していて、お使いのロジック アプリが [Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)に含まれている場合は、[Visual Studio を使用してロジック アプリを統合アカウントにリンクさせる](manage-logic-apps-with-visual-studio.md#link-integration-account)ことができます。

1. [Azure portal](https://portal.azure.com) で、既存のロジック アプリを開くか、新しいロジック アプリを作成します。

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。 **[統合アカウント]** で、 **[統合アカウントを選択してください]** の一覧を開き、目的の統合アカウントを選択します。

   ![統合アカウント メニューが表示された Azure portal を示すスクリーンショット。[ワークフロー設定] ペインが開いており、[統合アカウントを選択してください] の一覧が開いています。](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. リンクを終了するには、 **[保存]** を選択します。

   ![[ワークフロー設定] ペインと [保存] が選択されていることを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   統合アカウントが正常にリンクされると、Azure によって確認メッセージが表示されます。

   ![Azure の確認メッセージを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

これで、ロジック アプリが、統合アカウントと B2B コネクタで成果物を使用できるようになりました (XML 検証、フラット ファイルのエンコードまたはデコードなど)。  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>価格レベルの変更

統合アカウントの[制限](logic-apps-limits-and-config.md#integration-account-limits)を引き上げるために、より高い[価格レベル](#upgrade-pricing-tier)にアップグレードすることができます (使用可能な場合)。 たとえば、Free レベルから Basic レベルまたは Standard レベルにアップグレードできます。 また、使用可能な場合は、[下位レベルにダウングレード](#downgrade-pricing-tier)することもできます。 価格情報の詳細については、次のドキュメントをご確認ください。

* [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps の価格モデル](logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>価格レベルのアップグレード

この変更を行うには、Azure portal または Azure CLI のいずれかを使用できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

   Azure で、Azure サブスクリプションのすべての統合アカウントが表示されます。

1. **[統合アカウント]** で、移動する統合アカウントを選びます。 統合アカウント メニューで、 **[概要]** を選択します。

   ![統合アカウント メニューで [概要] が選択されている Azure portal を示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. [概要] ペインで、 **[価格レベルのアップグレード]** を選択します。これにより、利用可能な上位レベルがすべて一覧表示されます。 レベルを選択すると、変更がすぐに反映されます。

   ![[価格レベルのアップグレード] が選択された統合アカウントの [概要] ペインを示すスクリーンショット。](media/logic-apps-enterprise-integration-create-integration-account/upgrade-pricing-tier.png)

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI の前提条件をインストールします](/cli/azure/get-started-with-azure-cli) (まだインストールしていない場合)。

1. Azure portal で、[Azure Cloud Shell](../cloud-shell/overview.md) 環境を開きます。

   ![[Cloud Shell] が選択された Azure portal ツール バーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. コマンド プロンプトで [**az resource** コマンド](/cli/azure/resource#az_resource_update)を入力し、`skuName` を目的の上位レベルに設定します。

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   たとえば、Basic レベルの場合は、`skuName` を `Standard` に設定できます。

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>価格レベルのダウングレード

この変更を行うには、[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用します。

1. [Azure CLI の前提条件をインストールします](/cli/azure/get-started-with-azure-cli) (まだインストールしていない場合)。

1. Azure portal で、[Azure Cloud Shell](../cloud-shell/overview.md) 環境を開きます。

   ![[Cloud Shell] が選択された Azure portal ツール バーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. コマンド プロンプトで、[**az resource** コマンド](/cli/azure/resource#az_resource_update)を入力し、`skuName` を目的のレベルに設定します。

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   たとえば、Standard レベルを使用している場合は、`skuName` を `Basic` に設定できます。

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>ロジック アプリのリンクを解除する

ロジック アプリを別の統合アカウントにリンクする場合、またはロジック アプリで統合アカウントの使用を止める場合は、Azure Resource Explorer を使用してリンクを削除します。

1. ブラウザー ウィンドウを開き、[Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com) にアクセスします。 同じ Azure アカウントの資格情報でサインインします。

   ![Azure Resource Explorer が表示された Web ブラウザーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 検索ボックスで、ロジック アプリの名前を入力して検索し、ロジック アプリを開きます。

   ![ロジック アプリ名が入力されたエクスプローラーの検索ボックスを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. エクスプローラーのタイトル バーで、 **[読み取り/書き込み]** を選択します。

   ![[読み取り/書き込み] が選択されたタイトル バーを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **[データ]** タブで、 **[編集]** を選択します。

   ![[編集] が選択された [データ] タブを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. エディターで、`integrationAccount` オブジェクトを見つけて、そのプロパティを削除します。形式は以下のとおりです。

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   次に例を示します。

   !["integrationAccount" オブジェクトを見つける方法を示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. **[データ]** タブで **[Put]** を選択して変更を保存します。

   ![[Put] が選択された [データ] タブを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Azure Portal でロジック アプリを開きます。 ロジック アプリ メニューの **[ワークフロー設定]** で、 **[統合アカウント]** プロパティが空になっていることを確認します。

   ![ロジック アプリ メニューが表示され、[ワークフロー設定] が選択された Azure portal を示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>統合アカウントの移動

統合アカウントを別の Azure リソース グループまたは Azure サブスクリプションに移動できます。 リソースを移動すると、Azure によって新しいリソース ID が作成されるため、代わりに新しい ID を使用し、移動したリソースに関連付けられているスクリプトまたはツールを更新してください。 サブスクリプションを変更する場合は、既存のリソース グループまたは新しいリソース グループも指定する必要があります。

このタスクでは、このセクションの手順に従って Azure portal を使用するか、[Azure CLI](/cli/azure/resource#az_resource_move) のいずれかを使用できます。

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

   Azure で、Azure サブスクリプションのすべての統合アカウントが表示されます。

1. **[統合アカウント]** で、移動する統合アカウントを選びます。 統合アカウント メニューで、 **[概要]** を選択します。

1. [概要] ペインで、 **[リソース グループ]** または **[サブスクリプション名]** の横にある **[変更]** を選択します。

   ![[概要] ペインが表示された Azure portal を示すスクリーンショット。[リソース グループ] または [サブスクリプション名] の横にある [変更] が選択されています。](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 移動する関連リソースを選択します。

1. 選択した内容に基づいて、次の手順に従ってリソース グループまたはサブスクリプションを変更します。

   * リソース グループ: **[リソース グループ]** の一覧から、移動先のリソース グループを選択します。 または、別のリソース グループを作成するには、 **[新しいリソース グループの作成]** を選択します。

   * サブスクリプション: **[サブスクリプション]** の一覧から、移動先のサブスクリプションを選択します。 **[リソース グループ]** の一覧から、移動先のリソース グループを選択します。 または、別のリソース グループを作成するには、 **[新しいリソース グループの作成]** を選択します。

1. 移動したリソースに関連付けられているスクリプトやツールは、新しいリソース ID で更新されるまで機能しないことを理解していることを確認するために、[確認] ボックスを選択してから、 **[OK]** を選択します。

1. 完了したら、すべてのスクリプトを移動したリソースの新しいリソース ID で必ず更新してください。  

## <a name="delete-integration-account"></a>統合アカウントの削除

このタスクでは、このセクションの手順に従って Azure portal を使用するか、[Azure CLI](/cli/azure/resource#az_resource_delete)、または [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount) のいずれかを使用できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメインの検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

   Azure で、Azure サブスクリプションのすべての統合アカウントが表示されます。

1. **[統合アカウント]** で、削除する統合アカウントを選びます。 統合アカウント メニューで、 **[概要]** を選択します。

   ![[統合アカウント] の一覧と統合アカウント メニューが表示され、[概要] が選択されている Azure portal を示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. [概要] ウィンドウで、 **[削除]** を選択します。

   ![[概要] ペインで [削除] が選択されていることを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 統合アカウントを削除することを確認するために **[はい]** を選択します。

   ![確認ボックスで [はい] が選択されていることを示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

<a name="delete-account-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) コマンドを使用すると、統合アカウントを削除できます。

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

---

## <a name="next-steps"></a>次のステップ

* [統合アカウントで取引先を作成する](logic-apps-enterprise-integration-partners.md)
* [統合アカウントで取引先間の契約を作成する](logic-apps-enterprise-integration-agreements.md)
