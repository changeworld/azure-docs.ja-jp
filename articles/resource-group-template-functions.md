<properties
   pageTitle="Azure リソース マネージャーのテンプレートの関数"
   description="値の取得、文字列の書式設定、およびデプロイ情報の取得のために、Azure リソース マネージャーのテンプレートで使用する関数について説明します。"
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
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートの関数

このトピックでは、Azure リソース マネージャーのテンプレートで使用できるすべての関数について説明します。

## base64

**base64 (inputString)**

入力文字列の base64 表現を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| inputString | あり | Base 64 形式として返す文字列の値。

次の例は、base64 関数を使用する方法を示します。

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

## concat

**concat (arg1, arg2, arg3, ...)**

複数の文字列値を結合し、結果の文字列値を返します。この関数は、任意の数の引数を取ることができます。

次の例は、複数の値を結合して 1 つの値を返す方法を示しています。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

## デプロイ

**デプロイ()**

現在のデプロイ操作に関する情報を返します。

デプロイに関する情報が、次のプロパティを含むオブジェクトとして返されます。

    {
      "name": "",
      "properties": {
        "template": {},
        "parameters": {},
        "mode": "",
        "provisioningState": ""
      }
    }

次の例は、デプロイ情報を出力のセクションで返す方法を示します。

    "outputs": {
      "exampleOutput": {
        "value": "[deployment()]",
        "type" : "object"
      }
    }

## listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

ストレージ アカウントのキーを返します。resourceId は、[resourceId function](./#resourceid) を使用して、または形式 **providerNamespace/resourceType/resourceName** を使用して指定できます。関数を使用して、PrimaryKey と secondaryKey を取得できます。
  
| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| resourceName または resourceIdentifier | あり | ストレージ アカウントの一意の識別子。
| apiVersion | あり | リソースのランタイム状態の API バージョン。

次の例は、出力のセクションで、ストレージ アカウントからキーを返す方法を示しています。

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]", 
        "type" : "object" 
      } 
    } 

## padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。
  
| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToPad | あり | 右揃えにする文字列。
| totalLength | あり | 返される文字列の文字合計数。
| paddingCharacter | あり | 左余白の長さに到達するまで使用する文字。

次の例では、文字列が 10 文字に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値に埋め込む方法を示します。元のパラメーター値が 10 文字より長い場合、文字は追加されません。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }


## parameters

**parameters (parameterName)**

パラメーター値を返します。指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| parameterName | あり | 返されるパラメーターの名前。

次の例では、parameters 関数の簡単な使用方法を示しています。

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

## provider

**provider (providerNamespace, [resourceType])**

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。種類が指定されない場合は、サポートされているすべての種類が返されます。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| providerNamespace | あり | プロバイダーの名前空間
| resourceType | いいえ | 指定した名前空間内にあるリソースの種類。

サポートされている各種類は、次の形式で返されます。

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

次の例は、provider 関数の使用方法を示しています。

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

## reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

式が別のリソースのランタイム状態から値を取得できるようにします。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| resourceName または resourceIdentifier | あり | 名前またはリソースの一意の識別子。
| apiVersion | いいえ | リソースのランタイム状態の API バージョン。リソースが同じテンプレート内でプロビジョニングされていない場合は、パラメーターを使用する必要があります。

**reference** 関数はその値をランタイム状態から取得するので、変数セクションでは使用できません。これは、テンプレートの出力セクションで使用できます。

参照式を使用して、参照先のリソースが同じテンプレート内でプロビジョニングされる場合に、1 つのリソースが他のリソースに依存していることを宣言します。

    "outputs": {
      "siteUri": {
          "type": "string",
          "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
      }
    }

## replace

**replace(originalString, oldCharacter, newCharacter)**

指定された文字列内で、1 文字を別の文字で置き換えたすべてのインスタンスを含む新しい文字列を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| originalString | あり | 1 文字を別の文字で置き換えたすべてのインスタンスを含む文字列。
| oldCharacter | あり | 元の文字列から削除する文字。
| newCharacter | あり | 削除された文字の代わりに追加する文字。

次の例では、ユーザーが指定した文字列からすべてのダッシュ (-) を削除する方法を示します。

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

## resourceGroup

**resourceGroup()**

現在のリソース グループを表す、構造化オブジェクトを返します。オブジェクトは次の形式になります。

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

次の例では、リソース グループの場所を使用して、Web サイトの場所を割り当てます。

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

## resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

リソースの一意の識別子を返します。リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。識別子は、次の形式で返されます。

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| パラメーター | 必須 | 説明
| :---------------: | :------: | :----------
| resourceGroupName | いいえ | 省略可能なリソース グループの名前。既定値は、現在のリソース グループです。別のリソース グループ内のリソースを取得するときには、この値を指定します。
| resourceType | あり | リソース プロバイダーの名前空間を含むリソースの種類。
| resourceName1 | あり | リソースの名前。
| resourceName2 | いいえ | リソースが入れ子になっている場合、次のリソース名セグメント 。

次の例では、Web サイトとデータベースのリソース ID を取得する方法を示します。Web サイトは **myWebsitesGroup** という名前のリソース グループ内にあり、データベースはこのテンプレートの現在のリソース グループ内にあります。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。ストレージ アカウントや仮想ネットワークは、複数のリソース グループ間で使用される場合があるので、単一のリソース グループを削除するときにそれらを削除しないでください。次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }


## subscription

**subscription()**

サブスクリプションの詳細を次の形式で返します。

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

次の例は、出力セクションで呼び出される subscription 関数を示しています。

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 

## toLower

**toLower(stringToChange)**

指定された文字列を小文字に変換します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToChange | あり | 小文字に変換する文字列。

次の例では、ユーザー指定のパラメーター値を小文字に変換します。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

## toUpper

**toUpper(stringToChange)**

指定した文字列を大文字に変換します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToChange | あり | 大文字に変換する文字列。

次の例では、ユーザー指定のパラメーター値を大文字に変換します。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }


## variables

**variables (variableName)**

変数の値を返します。指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| variable Name | あり | 返す変数の名前。


## 次のステップ
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)
- [高度なテンプレートの操作](./resource-group-advanced-template.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](azure-portal/resource-group-template-deploy.md)
- [Azure リソース マネージャーの概要](./resource-group-overview.md)

<!---HONumber=July15_HO4-->