<properties
   pageTitle="リソースの複数のインスタンスのデプロイ | Microsoft Azure"
	description="Azure リソース マネージャー テンプレートで copy 操作と配列を使用して、リソースをデプロイする際に複数回反復処理する方法について説明します。"
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
	ms.date="08/27/2015"
	ms.author="tomfitz"/>

# Azure リソース マネージャーでリソースの複数のインスタンスを作成する

このトピックでは、Azure リソース マネージャー テンプレートで反復処理して、リソースの複数のインスタンスを作成する方法について説明します。

## copy、copyIndex、length

複数回作成するリソース内では、反復処理する回数を指定する **copy** オブジェクトを定義できます。copy は次の形式で指定します。

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

次の concat 関数内に示すように、現在の反復値には、**copyIndex()** 関数でアクセスできます。

    [concat('examplecopy-', copyIndex())]

値の配列から複数のリソースを作成する場合は、**length** 関数を使用して数を指定できます。length 関数のパラメーターとして、配列を指定します。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## name でのインデックス値の使用

copy 操作では、増分するインデックス値に基づいた一意の名前を付けたリソースの複数のインスタンスを作成できます。たとえば、デプロイ済みの各リソース名の末尾に一意の数値を追加することもできます。次の 3 つの Web サイトをデプロイするには:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

次のテンプレートを使用します。

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
   
コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに特に便利です。次の 3 つの Web サイトをデプロイするには:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

次のテンプレートを使用します。

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
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
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {} 
      } 
    ]

もちろん、コピー数を配列の長さ以外の値に設定します。たとえば、さまざまな値を含む配列を作成し、デプロイする配列要素の数を指定するパラメーターの値を渡すことができます。この場合は、最初の例に示すようにコピー数を設定します。

## 次のステップ
- テンプレートのセクションについては、「[Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートで使用できるすべての関数については、「[Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)」を参照してください。
- テンプレートをデプロイする方法については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](azure-portal/resource-group-template-deploy.md)」を参照してください。

<!---HONumber=September15_HO1-->