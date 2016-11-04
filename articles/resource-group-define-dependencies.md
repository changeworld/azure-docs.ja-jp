---
title: Resource Manager テンプレートでの依存関係 | Microsoft Docs
description: デプロイ時にリソースが正しい順序でデプロイされるように、あるリソースが別のリソースに依存するように設定する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2016
ms.author: tomfitz

---
# Azure リソース マネージャーのテンプレートでの依存関係の定義
リソースによっては、デプロイする前に、他のリソースが存在している必要がある場合があります。たとえば、SQL データベースをデプロイするには、先に SQL Server が存在している必要があります。このリレーションシップは、一方のリソースがもう一方のリソースに依存しているとマークすることで定義します。通常、依存関係を定義するには **dependsOn** 要素を使用しますが、**reference** 関数を使用することもできます。

Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。相互依存していないリソースは、平行してデプロイされます。

## dependsOn
テンプレート内で dependsOn 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。その値には、リソース名のコンマ区切りリストを指定できます。

次の例では、ロード バランサー、仮想ネットワーク、および複数のストレージ アカウントを作成するループに依存する仮想マシン スケール セットを示します。こうした他のリソースは、次の例には示されていませんが、テンプレートの他の場所に存在する必要があります。

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

リソースとコピー ループで作成されたリソースの間に依存関係を定義するには、dependsOn 要素をループの名前に設定します。例が必要であれば、「[Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

dependsOn を使用してリソース間のリレーションシップをマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。これは、デプロイのパフォーマンスに影響を与える可能性があるためです。たとえば、リソースが相互にどのように接続されているかをドキュメント化するには、dependsOn は適切な方法ではありません。どのリソースが dependsOn 要素で定義されたかを、デプロイ後に照会することはできません。Resource Manager では、依存関係のある 2 つのリソースが並列でデプロイされないため、dependsOn を使用すると、デプロイ時間に影響を及ぼす可能性があります。リソース間のリレーションシップをドキュメント化するには、代わりに[リソース リンク](resource-group-link-resources.md)を使用します。

## 子リソース
resources プロパティを使用すると、定義されているリソースに関連する子リソースを指定できます。子リソースの定義の深さは 5 レベルまでです。重要なのは、子リソースと親リソースの間に暗黙的な依存関係を作成しないことです。親リソースの後に子リソースをデプロイする必要がある場合は、dependsOn プロパティを使用してその依存関係を明示的に指定する必要があります。

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。許容されるリソースの種類は、親リソースの[テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas)で指定されます。子リソースの種類の名前には、親リソースの種類の名前 (**Microsoft.Web/sites/config**、**Microsoft.Web/sites/extensions** など。これは両方とも **Microsoft.Web/sites** の子リソース)。

次の例では、SQL Server と SQL データベースを示します。データベースが SQL Server の子である場合でも、SQL データベースと SQL Server の間に明示的な依存関係が定義されることに注目してください。

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## reference 関数
[reference 関数](resource-group-template-functions.md#reference)を使用すると、式では、他の JSON の名前と値のペアまたはランタイム リソースからその値を導出することができます。参照式では、あるリソースが別のリソースに依存することを暗黙的に宣言します。

    reference('resourceName').propertyPath

この要素または dependsOn 要素のいずれかを使用して依存関係を指定できますが、同じ依存リソースに両方の要素を使用する必要はありません。不要な依存関係が誤って追加されないように、できる限り、暗黙的な参照を使用してください。

詳細については、「[reference 関数](resource-group-template-functions.md#reference)」を参照してください。

## 次のステップ
* Azure リソース マネージャーのテンプレートの作成の詳細については、[テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* テンプレートで使用可能な関数の一覧については、[テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。

<!---HONumber=AcomDC_0914_2016-->