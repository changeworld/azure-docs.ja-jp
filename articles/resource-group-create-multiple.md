<properties
   pageTitle="リソースの複数のインスタンスの作成"
   description="Azure リソース マネージャー テンプレートで copy 操作を使用して、リソースをデプロイする際に複数回反復処理する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーでリソースの複数のインスタンスを作成する

このトピックでは、Azure リソース マネージャー テンプレートで反復処理して、リソースの複数のインスタンスを作成する方法について説明します。

## copy と copyIndex()

複数回作成するリソース内では、反復処理する回数を指定する **copy** オブジェクトを定義できます。copy は次の形式で指定します。

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

次の concat 関数内に示すように、現在の反復値には、**copyIndex()** 関数でアクセスできます。

    [concat('examplecopy-', copyIndex())]

## name でのインデックス値の使用

copy 操作では、増分するインデックス値に基づいた一意の名前を付けたリソースの複数のインスタンスを作成できます。たとえば、デプロイ済みの各リソース名の末尾に一意の数値を追加することもできます。次の 3 つの Web サイトをデプロイするとします。

- examplecopy-0
- examplecopy-1
- examplecopy-2.

この場合は、次のテンプレートを使用します。

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## インデックス値のオフセット

前述の例では、インデックス値が 0 から 2 に増えることがわかります。インデックス値をオフセットするには、**copyIndex(1)** のように、**copyIndex()** 関数に値を渡します。実行する反復処理の数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。そのため前の例と同じテンプレートを使用しても、**copyIndex(1)** を指定すると、次の名前の 3 つの Web サイトがデプロイされます。

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 配列の使用
   
コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに特に便利です。次の 3 つの Web サイトをデプロイするとします。

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

この場合は、次のテンプレートを使用します。

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## 次のステップ
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](azure-portal/resource-group-template-deploy.md)

<!---HONumber=July15_HO4-->