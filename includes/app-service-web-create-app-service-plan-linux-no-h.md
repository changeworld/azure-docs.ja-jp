---
title: "インクルード ファイル" description: "インクルード ファイル" services: app-service author: cephalin ms.service: app-service ms.topic: "インクルード" ms.date: 05/02/2021 ms.author: cephalin ms.custom: "インクルード ファイル", devx-track-azurecli
---

Cloud Shell で [`az appservice plan create`](/cli/azure/appservice/plan) コマンドを使用して、App Service プランを作成します。

<!-- [!INCLUDE [app-service-plan](app-service-plan.md)] -->

次の例では、**Free** 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE --is-linux
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

<pre>
{ 
  "freeOfferExpirationTime": null,
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>
