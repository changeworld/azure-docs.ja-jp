---
title: シングルテナント ワークフローの値のパラメーターを作成する
description: シングルテナント Azure Logic Apps のデプロイ環境間で異なるワークフローの値のパラメーターを定義します。
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 08/09/2021
ms.openlocfilehash: a29eda23d12ca07057ff1081ae8d9bc4cfdc56ed
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137032"
---
# <a name="create-parameters-to-use-in-workflows-across-environments-in-single-tenant-azure-logic-apps"></a>シングルテナント Azure Logic Apps の環境をまたいだワークフローで使用するパラメーターを作成する

Azure Logic Apps では、"*パラメーター*" を定義することによって、開発、テスト、および運用環境で、ワークフローで変化する可能性のある値を抽象化できます。 パラメーターを代わりに使用すると、ワークフローの設計に専念し、環境固有の変数を後で挿入することができます。

この記事では、シングルテナント Azure Logic Apps でパラメーターがどのように機能するか、また、シングルテナントのパラメーター エクスペリエンスを使用して環境変数を編集、参照、および管理する方法について説明します。

<a name="parameters-introduction"></a>

## <a name="parameters-in-single-tenant-versus-multi-tenant"></a>シングルテナントとマルチテナントのパラメーター

"*マルチテナント*" の Azure Logic Apps を使用したことがある場合は、既にパラメーターに精通されているかもしれません。 シングルテナント Azure Logic Apps でのワークフロー入力のパラメーター化は、マルチテナント Azure Logic Apps の場合と似ていますが、大きな違いがあります。

たとえば、シングルテナントとマルチテナントの両方のサービスで、ワークフロー デザイナーで作業しているときにパラメーターを定義できます。 パラメーターを定義した後は、"*同じ*" ロジック アプリ リソース内の任意のワークフローまたは接続からそのパラメーターを参照できます。 ただし、"*マルチテナント*" サービスでは、デザイナーでパラメーターを作成して使用した後、Azure Resource Manager テンプレート (ARM テンプレート) とテンプレート パラメーター ファイルで環境変数を定義し、設定します。 このシナリオでは、"*デプロイ時に*" パラメーターを定義して設定する必要があるため、1 つの変数を変更するだけでも、ロジック アプリの ARM テンプレートを再デプロイする必要があります。

それに比べて、"*シングルテナント*" サービスでは、パラメーター "*と*" アプリ設定を使用して、実行時とデプロイ時の両方で環境変数を操作できます。 シングルテナントの Azure Logic Apps では、アプリ設定に、同じロジック アプリ内の "*すべてのワークフロー*" に適用されるグローバル構成オプションが含まれています。 詳細については、[シングルテナントベースのロジック アプリのホストとアプリ設定の編集](edit-app-settings-host-settings.md)に関するページを参照してください。

たとえば、アプリ設定を使用して Azure Key Vault と統合し、接続文字列やキーなどの[セキュリティで保護された文字列を直接参照](../app-service/app-service-key-vault-references.md)することができます。 デプロイ時に環境変数を定義できる ARM テンプレートと同様に、[ロジック アプリのワークフロー定義](/azure/templates/microsoft.logic/workflows)内でアプリ設定を定義できます。 その後、動的に生成されたインフラストラクチャ値 (接続エンドポイント、ストレージ文字列など) を取得できます。

ただし、アプリ設定にはサイズ制限があり、Azure Logic Apps の特定の領域からは参照できません。 パラメーターでは、大きな値のサイズや複雑なオブジェクトのサポートなど、アプリ設定よりも幅広いユース ケースが提供されます。

たとえば、ローカル開発ツールとして Visual Studio Code を使用してワークフローをローカルで実行する場合、ロジック アプリ プロジェクトでは、**parameters.json** ファイルを使用してパラメーターを定義できます。 その後、プロジェクトの **workflow.json** ファイルの任意のワークフローや、プロジェクトの **connections.json** ファイル内の任意の接続オブジェクトから、このパラメーター ファイル内の任意のパラメーターを参照できます。 次の一覧に、一般的なユースケースをいくつか示します。

* テスト時に使用するすべての値を含むテスト パラメーター ファイルを用意します。 デプロイ時に、テスト パラメーター ファイルを運用パラメーター ファイルに置き換えることができます。

* **connections.json** ファイルのさまざまな部分をパラメーター化します。 その後、**connections.json** ファイルをソース管理にチェックインし、**parameters.json** ファイルを介して接続を管理できます。

* `authentication` JSON オブジェクトなどの複雑なオブジェクトをパラメーター化します。 たとえば、`authentication` オブジェクトの値を、単一のパラメーター式 (`@parameters('api-auth')`など) を保持する文字列に置き換えることができます。

* プロジェクトの **local.settings.json** ファイルでアプリ設定を確認し、編集します。 その後、これらのアプリ設定をパラメーターで参照できます。

> [!NOTE]
> 一般的な推奨事項として、値をパラメーター化する既定の方法として、アプリ設定ではなく、パラメーターを使用することを検討してください。 そうすることで、セキュリティで保護されたキーや文字列を格納する必要がある場合、推奨事項に従って、パラメーターからアプリ設定を参照できます。 必要に応じて、両方のオプションをソリューションで使用するには、パラメーターを使用してアプリ設定を参照します。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* [シングルテナントの Azure Logic Apps でホストされているロジック アプリ ワークフロー](single-tenant-overview-compare.md)。

  ロジック アプリがない場合は、[Azure portal](create-single-tenant-workflows-azure-portal.md) または [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) でロジック アプリ (標準) を作成してください。

## <a name="define-use-and-edit-parameters"></a>パラメーターの定義、使用、および編集

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) で、シングルテナント ベースのロジック アプリを開きます。 **[ワークフロー]** で、ワークフローを選択してデザイナーで開きます。

1. デザイナーのツール バーから、 **[パラメーター]** を選択します。

   ![Azure portal、ワークフロー デザイナー、およびデザイナー ツールバーの "パラメーター" が選択された状態を示すスクリーンショット。](./media/parameterize-workflow-app/portal-designer-select-parameters.png)

1. **[パラメーター]** ペインで、 **[パラメーターの作成]** を選択します。

1. 作成するパラメーターに関する次の情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **名前** | はい | 作成するパラメーターの名前です。 |
   | **Type** | はい | パラメーターのデータ型。**Array**、**Bool**、**Float**、**Int**、**Object**、**String** などです。 <p><p>**注**: シングルテナントベースのワークフローでは、`securestring` や `secureobject` などのセキュリティで保護されたデータ型はサポートされていません。 |
   | **Value** | はい | パラメーターの値。 <p><p>シングルテナント Azure Logic Apps では、ワークフロー ロジック、接続情報、およびパラメーター値が 1 つの場所に存在しないため、パラメーター値を指定する必要があります。 デザイナーは、読み込み前にパラメーター値を解決できる必要があります。 |
   |||

   次の例は、文字列パラメーターの定義を示しています。

   ![Azure portal、ワークフロー デザイナー、およびパラメーター定義の例を含む "パラメーター" ペインを示すスクリーンショット。](./media/parameterize-workflow-app/define-parameter.png)

1. 完了したら、 **[パラメーター]** ペインを閉じますが、新しいパラメーターの定義を保存するために、必ずワークフローを保存してください。

1. 同じロジック アプリ内の任意のワークフローに含まれるトリガーまたはアクションからパラメーターを参照するには、これらの手順を実行します。

   1. デザイナーで、目的のワークフローを開き、トリガーまたはアクションを展開します。

   1. パラメーターを使用するプロパティで、そのプロパティの編集ボックス内をクリックします。

   1. 開いた動的なコンテンツの一覧で、 **[パラメーター]** の下の以前に作成したパラメーターを選択します。次に例を示します。

      ![カーソルをプロパティ編集ボックスに置き、動的コンテンツ リストを展開し、以前に作成したパラメーターを選択したアクション例を示すスクリーンショット。](./media/parameterize-workflow-app/reference-parameter.png)

1. パラメーターを同じロジック アプリで表示または編集するには、次のいずれかの手順に従います。

   * そのロジック アプリで任意のワークフローを開きます。 ワークフロー メニューで、 **[デザイナー]** を選択します。 デザイナーのツール バーで、 **[パラメーター]** を選択します。

     **[パラメーター]** ペインが開き、そのロジック アプリのワークフローから定義したすべてのパラメーターが表示されます。

   * 一括での JSON の表示または編集を行うには、ロジック アプリのメインメニューで、 **[パラメーター]** を選択します。

     **[パラメーター]** JSON ビューが開き、そのロジック アプリのワークフローから定義したすべてのパラメーターが表示されます。

### <a name="visual-studio-code"></a>Visual Studio Code

1. **parameters.json** という名前のプロジェクトのルートレベルの JSON ファイルで、"*すべて*" のパラメーターとその値を定義します。 このファイルには "*キーと値*" のペアを含むオブジェクトがあります。 各 "*キー*" は各パラメーターの名前であり、各 "*値*" は各パラメーターの構造体です。 各構造体には、`type` と `value` の両方の宣言を含める必要があります。

   > [!IMPORTANT]
   > **parameters.json** ファイルには、ワークフローの定義や接続など、プロジェクト内の他の場所で参照または使用するすべてのパラメーターとその値を定義し、含める必要があります。

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
                "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey>')" 
            }
        }
    }
    ```

    > [!NOTE]
    > **parameters.json** ファイルでは、`@appsetting` が唯一有効な式の型です。

1. トリガーまたはアクション入力でパラメーターを参照するには、式 `@parameters('<parameter-name>')` を使用します。

#### <a name="parameterize-connections-file"></a>接続ファイルのパラメーター化

**connections.json** ファイルをパラメーター化するには、`ConnectionRuntimeUrl` などのリテラル値を単一の `parameters()` 式 (たとえば、`@parameters('api-runtimeUrl')`) に置き換えます。 **connections.json** ファイルで、有効な式の型は `@parameters` と `@appsetting` だけです。

> [!IMPORTANT]
>
> 開発時に **connections.json** ファイルをパラメーター化すると、ローカルと Azure portal の両方でデザイナー エクスペリエンスが制限されます。 デザイナーを開発に使用する必要がある場合は、パラメーター化されていない **connections.json** ファイルを代わりに使用してください。 その後、デプロイ パイプラインで、パラメーター化されたファイルに置き換えます。 
> この場合でも、ランタイムはパラメーター化で動作します。 デザイナーの機能強化は、現在開発中です。

次の例では、アプリ設定とパラメーターの両方を使用する、パラメーター化された **connections.json** ファイルを示します。 可能な限りパラメーターを使用したいところですが、このシナリオは例外またはエッジ ケースになります。ここでパラメーターではなくアプリ設定を使用するのは、アプリ設定がデプロイ時に生成され、開発パイプラインでデータを動的に設定しやすいためです。 このサンプル ファイルでは、複雑な `blob_auth` 認証オブジェクトにパラメーターを使用し、その他の値にアプリ設定を使用しています。 この場合、パラメーターをワークフローで参照することがほとんどないため、認証オブジェクトにパラメーターを使用できます。

```json
{
   "serviceProviderConnections": {
      "serviceBus": {
         "parameterValues": {
            "connectionString": "@appsetting('serviceBus_connectionString')"
        },
        "serviceProvider": {
           "id": "/serviceProviders/serviceBus"
        },
        "displayName": "servicebus"
     }
   },
   "managedApiConnections": {
      "azureblob": {
         "api": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/providers/Microsoft.Web/locations/@{appsetting('WORKFLOWS_LOCATION_NAME')}/managedApis/azureblob"
         },
         "connection": {
            "id": "/subscriptions/@{appsetting('WORKFLOWS_SUBSCRIPTION_ID')}/resourceGroups/@{appsetting('WORKFLOWS_RESOURCE_GROUP_NAME')}/providers/Microsoft.Web/connections/azureblob"
         },
         "connectionRuntimeUrl": "@appsetting('BLOB_CONNECTION_RUNTIMEURL')",
         "authentication": "@parameters('blob_auth')"
      }
   }
}
```

> [!NOTE]
> プレーンテキストでインラインの式がある場合は、その式を中かっこ ({}) で囲み、その式の補間形式を使用してください。 この形式は、解析の問題を回避するのに役立ちます。
>
> たとえば、`"<text>/@<function-name>('<parameter-name>')/<text>"` がある場合は、代わりに次のバージョンを使用します。 `"<text>/@{<function-name>('<parameter-name>')}/<text>"`。 
>
> 詳細については、「[関数の使用に関する考慮事項](workflow-definition-language-functions-reference.md#function-considerations)」を参照してください。
     

## <a name="manage-parameters-files"></a>パラメーター ファイルを管理する

通常、複数のバージョンのパラメーター ファイルを管理する必要があります。 開発、テスト、運用などのデプロイ環境に応じて、さまざまな目標値が設定されている場合があります。 多くの場合、これらのパラメーター ファイルの管理は、ARM テンプレート パラメーター ファイルの管理と同様です。 特定の環境にデプロイする場合、通常は DevOps のパイプラインを通じて、対応するパラメーター ファイルを昇格させます。

Azure CLI を使用してパラメーター ファイルを動的に置き換えるには、次のコマンドを実行します。

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

NuGet ベースのロジック アプリ プロジェクトがある場合は、プロジェクト ファイル ( **&lt;ロジック アプリ名&gt;.csproj**) を更新し、次のようにビルド出力にパラメーター ファイルを含める作業が必要です。

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

* [シングルテナント ベースのロジック アプリ用の DevOps デプロイの概要](devops-deployment-single-tenant-azure-logic-apps.md)
* [シングルテナント ベースのロジック アプリ用の DevOps デプロイを設定する](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>アプリ設定を管理する

シングルテナントの Azure Logic Apps では、アプリ設定に、同じロジック アプリ内の "*すべてのワークフロー*" に適用されるグローバル構成オプションが含まれています。 ワークフローを Visual Studio Code でローカルに実行すると、これらのアプリ設定に、**local.settings.json** ファイル内のローカル環境変数としてアクセスできます。 その後、これらのアプリ設定をパラメーターで参照できます。

アプリ設定を追加、更新、または削除するには、Visual Studio Code、Azure portal、Azure CLI、または ARM (Bicep) テンプレートに関する以下のセクションを選択して確認してください。

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

ロジック アプリに使用するアプリ設定を Azure portal で確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、シングルテナント ベースのロジック アプリを開きます。

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
