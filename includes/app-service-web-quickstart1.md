## <a name="create-a-resource-group"></a>リソース グループの作成

 [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)は、Web アプリやデータベースなどのリソースを管理する論理コンテナーです。 リソース グループ内のリソースはデプロイ、更新、削除されます。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

利用可能な場所を表示するには、`az appservice list-locations` コマンドを使用します。 一般に、リソースは、近くのリージョンに作成します。

## <a name="create-an-azure-app-service-plan"></a>Azure App Service プランの作成

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、"無料の" [App Service プラン](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。

[!INCLUDE [app-service-plan](app-service-plan.md)]

次のコマンドでは、**Free** 価格レベルを使用して、`quickStartPlan` という名前の App Service プランを作成します。

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Web アプリの作成