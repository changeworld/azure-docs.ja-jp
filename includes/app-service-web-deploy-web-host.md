### App Service プラン

Web アプリをホストするためのサービス プランを作成します。**hostingPlanName** パラメーターに、プランの名前を指定します。プランの場所は、リソース グループに使用される場所と同じになります。価格レベルとワーカー サイズを **sku** パラメーター と **workerSize** パラメーターに指定します。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

