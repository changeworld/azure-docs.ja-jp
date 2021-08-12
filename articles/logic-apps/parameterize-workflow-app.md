---
title: シングルテナントの Azure Logic Apps でワークフローのパラメーターを作成する
description: シングルテナントの Azure Logic Apps で、デプロイ環境間でワークフローが異なる値のパラメーターを定義します。
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 7de89605f86e47b3062ec07160288ab14584f42e
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747421"
---
# <a name="create-parameters-for-values-that-change-in-workflows-across-environments-for-single-tenant-azure-logic-apps"></a>シングルテナントの Azure Logic Apps で、環境間でワークフローが変わる値のパラメーターを作成する

Azure Logic Apps では、パラメーターを使用して、環境間で変わる可能性のある値を抽象化できます。 ワークフローで使用するパラメーターを定義することで、最初はワークフローの設計に集中し、その後で環境固有の変数を挿入できます。

"*マルチテナント*" の Azure Logic Apps では、ワークフロー デザイナーでパラメーターを作成して参照し、Azure Resource Manager (ARM) テンプレートとパラメーター ファイルで変数を設定することができます。 パラメーターは、デプロイ時に定義および設定されます。 そのため、1 つの変数のみを変更する必要がある場合でも、ロジック アプリの ARM テンプレートを再デプロイする必要があります。

"*シングルテナント*" の Azure Logic Apps では、パラメーターとアプリ設定を使用して、実行時とデプロイ時の両方で環境変数を操作できます。 この記事では、新しいシングルテナント パラメーター エクスペリエンスを使用して、環境変数の編集、呼び出し、参照を行う方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- [シングルテナントの Azure Logic Apps でホストされているロジック アプリ ワークフロー](single-tenant-overview-compare.md)。

  ロジック アプリがない場合は、[Azure portal](create-single-tenant-workflows-azure-portal.md) または [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) でロジック アプリ (標準) を作成してください。

## <a name="parameters-versus-app-settings"></a>パラメーターとアプリ設定

環境変数の格納場所を決定する前に、次の情報を確認してください。

Azure Functions または Azure Web Apps を既に使用している場合は、アプリ設定に精通されているかもしれません。 Azure Logic Apps では、アプリ設定が Azure Key Vault と統合されており、 接続文字列やキーなど、[セキュリティで保護された文字列を直接参照](../app-service/app-service-key-vault-references.md)できます。 デプロイ時に環境変数を定義できる ARM テンプレートと同様に、[ロジック アプリのワークフロー定義](/azure/templates/microsoft.logic/workflows)内でアプリ設定を定義できます。 その後、動的に生成されたインフラストラクチャ値 (接続エンドポイント、ストレージ文字列など) を取得できます。 ただし、アプリ設定にはサイズの制限があり、Azure Logic Apps の特定の領域からは参照できません。

マルチテナントの Azure Logic Apps でのワークフローに精通している場合は、パラメーターにも精通されているかもしれません。 パラメーターは、複合オブジェクトや大きいサイズの値のサポートなど、アプリ設定よりも幅広い範囲のユース ケースで使用できます。 ローカルの開発ツールとして Visual Studio Code を使用する場合は、ロジック アプリ プロジェクトの **workflow.json** および **connections.json** ファイルのパラメーターも参照できます。 ソリューションで両方のオプションを使用する場合は、パラメーターを使用してアプリ設定を参照することもできます。

> [!NOTE]
> 開発の場合、**connections.json** ファイルをパラメーター化すると、ローカルと Azure portal の両方でデザイナー エクスペリエンスが制限されます。 デザイナーを開発に使用する必要がある場合は、パラメーター化されていない **connections.json** ファイルを使用してください。 その後、デプロイ パイプラインで、パラメーター化されたファイルに置き換えます。 この場合でも、ランタイムはパラメーター化で動作します。 デザイナーの機能強化は、現在開発中です。

パラメーターをパラメーター化の既定のメカニズムとして使用するための推奨事項について検討してください。 そうすることで、セキュリティで保護されたキーや文字列を格納する必要がある場合、推奨事項に従って、パラメーターからアプリ設定を参照できます。

## <a name="what-is-parameterization"></a>パラメーター化とは

ロジック アプリ プロジェクトで Visual Studio Code を使用する場合、**parameters.json** ファイルでパラメーターを定義できます。 **parameters.json** ファイル内のパラメーターはすべて、ロジック アプリ内の任意のワークフローまたは接続オブジェクトから参照できます。 シングルテナント Azure Logic Apps でのワークフロー入力のパラメーター化は、マルチテナント Azure Logic Apps の場合と同様です。

トリガーまたはアクション入力でパラメーターを参照するには、式 `@parameters('<parameter-name>')` を使用します。

> [!IMPORTANT]
> 参照するパラメーターを **parameters.json** ファイルにも含めるようにしてください。

"*シングルテナント*" の Azure Logic Apps では、**connections.json** ファイルのさまざまな部分をパラメーター化できます。 その後、**connections.json** ファイルをソース管理にチェックインし、**parameters.json** ファイルを介して接続を管理できます。 **connections.json** ファイルをパラメーター化するには、`ConnectionRuntimeUrl` などのリテラル値を単一の `parameters()` 式 (たとえば、`@parameters('api-runtimeUrl')`) に置き換えます。

`authentication` JSON オブジェクトなどの複合オブジェクトをパラメーター化することもできます。 たとえば、`authentication` オブジェクトの値を、単一のパラメーター式 (`@parameters('api-auth')`など) を保持する文字列に置き換えます。 

> [!NOTE]
> **connections.json** ファイルで有効な式の型は、`@parameters` と `@appsetting` のみです。

## <a name="define-parameters"></a>パラメーターを定義する

シングルテナント ベースのワークフローでは、すべてのパラメーター値を、**parameters.json** という名前のルートレベルの JSON ファイルに格納する必要があります。 このファイルには、キーと値のペアを含むオブジェクトが含まれています。 キーは各パラメーターの名前で、値は各パラメーターの構造体です。 各構造体には、`type` と `value` の両方の宣言を含める必要があります。

> [!NOTE]
> **parameters.json** ファイルで有効な式の型は、`@appsetting`のみです。

次の例は、基本的なパラメーター ファイルを示しています。

```json
{ 
    "responseString": { 
        "type": "string", 
        "value": "hello" 
    }, 
    "functionAuth": { 
        "type": "object", 
        "value": { 
            "type": "QueryString", 
            "name": "Code", 
            "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey')" 
        } 
    } 
} 
```

通常、複数のバージョンのパラメーター ファイルを管理する必要があります。 開発、テスト、運用などのデプロイ環境に応じて、さまざまな目標値が設定されている場合があります。 多くの場合、これらのパラメーター ファイルの管理は、ARM テンプレート パラメーター ファイルの管理と同様です。 特定の環境にデプロイする場合、通常は DevOps のパイプラインを通じて、対応するパラメーター ファイルを昇格させます。

Azure CLI を使用してパラメーター ファイルを動的に置き換えるには、次のコマンドを実行します。

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

NuGet ベースのロジック アプリ プロジェクトがある場合は、プロジェクト ファイル ( **&lt;ロジックアプリ名&gt;.csproj**) を更新し、次のようにビルド出力にパラメーター ファイルを含める作業が必要です。
  
```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> 現時点ではまだ、パラメーター ファイルを動的に置き換える機能を Azure portal およびワークフロー デザイナーで使用することはできません。

DevOps デプロイ用にロジック アプリを設定する方法の詳細については、次のドキュメントを参照してください。

- [シングルテナント ベースのロジック アプリ用の DevOps デプロイの概要](devops-deployment-single-tenant-azure-logic-apps.md)
- [シングルテナント ベースのロジック アプリ用の DevOps デプロイを設定する](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>アプリ設定を管理する

シングルテナントの Azure Logic Apps では、アプリ設定に、同じロジック アプリ内の "*すべてのワークフロー*" に適用されるグローバル構成オプションが含まれています。 ワークフローを Visual Studio Code でローカルに実行すると、これらの設定を、**local.settings.json** ファイル内のローカル環境変数としてアクセスできます。 その後、これらのアプリ設定をパラメーターで参照できます。

アプリ設定を追加、更新、または削除するには、Visual Studio Code、Azure portal、Azure CLI、または ARM (Bicep) テンプレートに関する以下のセクションを選択して確認してください。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

ロジック アプリに使用するアプリ設定を Azure portal で確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の検索ボックスでシングルテナント ベースのロジック アプリを検索し、開きます。
1. ロジック アプリのメニューにある **[設定]** で、 **[構成]** を選択します。
1. **[構成]** ページの **[アプリケーション設定]** タブで、ロジック アプリのアプリ設定を確認します。
1. すべての値を表示するには、 **[値を表示する]** を選択します。 または、1 つの値を表示するには、その値を選択します。

新しい設定を追加するには、次の手順に従います。

1. **[アプリケーション設定]** タブの **[アプリケーション設定]** で、 **[新しいアプリケーション設定]** を選択します。
1. **[名前]** に、新しい設定の "*キー*" または名前を入力します。
1. **[値]** には、新しい設定の値を入力します。
1. 新しい "*キーと値*" のペアを作成する準備ができたら、 **[OK]** を選択します。

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="シングルテナント ベースのロジック アプリ用のアプリ設定が表示された Azure portal と構成ウィンドウを示すスクリーンショット。" lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して現在のアプリ設定を確認するには、コマンド `az logicapp config appsettings list` を実行します。 このコマンドに `--name -n` および `--resource-group -g` パラメーターを必ず含めてください。次に例を示します。

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Azure CLI を使用してアプリ設定を追加または更新するには、コマンド `az logicapp config appsettings set` を実行します。 このコマンドに `--name n` および `--resource-group -g` パラメーターを必ず含めてください。 たとえば、次のコマンドを使用して、`CUSTOM_LOGIC_APP_SETTING` という名前のキーと値 `12345` を持つ設定を作成します。

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[Resource Manager または Bicep テンプレート](#tab/azure-resource-manager)

アプリ設定を ARM テンプレートまたは Bicep テンプレートで確認および定義するには、ロジック アプリのリソース定義を見つけて、`appSettings` JSON オブジェクトを更新します。 リソース定義の詳細については、[ARM テンプレートのリファレンス](/azure/templates/microsoft.web/sites)を参照してください。

次の例は、ARM テンプレートまたは Bicep テンプレートのファイル設定を示しています。

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](single-tenant-overview-compare.md)
