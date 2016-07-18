<properties
   pageTitle="リソース マネージャーのテンプレートの関数 | Microsoft Azure"
   description="値の取得、文字列の処理、デプロイ情報の取得のために、Azure リソース マネージャーのテンプレートで使用する関数について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2016"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートの関数

このトピックでは、Azure リソース マネージャーのテンプレートで使用できるすべての関数について説明します。

テンプレート関数とそのパラメーターでは大文字と小文字が区別されません。たとえば、Resource Manager では、**variables('var1')** と **VARIABLES('VAR1')** が同じものとして解決されます。評価の際、関数は、大文字/小文字を明確に変更する (toUpper、toLower など) 場合を除き、大文字/小文字を保持します。特定のリソースの種類では、関数の評価方法とは無関係に、大文字/小文字の要件が存在する場合があります。

## 数値関数

リソース マネージャーには、整数を操作する次の関数が用意されています。

- [追加](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [length](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### add

**add(operand1, operand2)**

指定された 2 つ整数の合計を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1 | あり | 使用する最初のオペランドです。
| operand2 | あり | 使用する 2 番目のオペランドです。


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

反復処理のループの現在のインデックスを返します。

この関数は常に **copy** オブジェクトと共に使用されます。**copyIndex** を使用する例については、「[Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。


<a id="div" />
### div

**div(operand1, operand2)**

指定された 2 つの整数の整数除算を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1 | あり | 割られる数。
| operand2 | あり | 割る数。ゼロ (0) 以外にする必要があります。


<a id="int" />
### int

**int(valueToConvert)**

指定された値を整数に変換します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| valueToConvert | あり | 整数に変換する値。値の種類は文字列か整数になります。

次の例では、ユーザー指定のパラメーター値を整数に変換します。

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

指定された 2 つの整数を使用した整数除算の剰余を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1 | あり | 割られる数。
| operand2 | あり | 割る数。ゼロ (0) 以外にする必要があります。



<a id="mul" />
### mul

**mul(operand1, operand2)**

指定された 2 つの整数の乗算を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1 | あり | 使用する最初のオペランドです。
| operand2 | あり | 使用する 2 番目のオペランドです。


<a id="sub" />
### sub

**sub(operand1, operand2)**

指定された 2 つの整数の減算を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| operand1 | あり | 減算される数値。
| operand2 | あり | 減算する数値。


## 文字列関数

リソース マネージャーには、文字列を操作する次の関数が用意されています。

- [base64](#base64)
- [concat](#concat)
- [length](#length)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [string](#string)
- [substring](#substring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [trim](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)

文字列または配列の文字数を取得する方法については、「[length](#length)」を参照してください。

<a id="base64" />
### base64

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

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

複数の値を結合し、連結された結果を返します。この関数は、任意の数の引数を取ることができ、パラメーターに文字列または配列を使用できます。

次の例は、複数の文字列値を結合して 1 つの連結文字列を返す方法を示しています。

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

次の例は、2 つの配列を結合する方法を示しています。

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="padleft" />
### padLeft

**padLeft(valueToPad, totalLength, paddingCharacter)**

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。
  
| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| valueToPad | はい | 右揃えにする文字列または整数。
| totalLength | あり | 返される文字列の文字合計数。
| paddingCharacter | いいえ | 左余白の長さに到達するまで使用する文字。既定値は空白です。

次の例では、文字列が 10 文字に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値に埋め込む方法を示します。元のパラメーター値が 10 文字より長い場合、文字は追加されません。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

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

<a id="split" />
### split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

送信された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| inputString | あり | 分割する文字列。
| delimiter | あり | 使用する区切り記号。1 つの文字列または文字列の配列を指定できます。

次の例では、入力文字列をコンマで分割します。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### string

**string(valueToConvert)**

指定された値を文字列に変換します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| valueToConvert | はい | 文字列に変換する値。オブジェクトと配列を含む、あらゆる種類の値を変換できます。

次の例では、ユーザー指定のパラメーター値を文字列に変換します。

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

指定した文字位置から始まる指定された文字数分の部分文字列を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToParse | あり | 部分文字列の抽出元となる文字列。
| startIndex | いいえ | 部分文字列の 0 から始まる開始文字位置。
| length | いいえ | 部分文字列の文字数。

次の例では、パラメーターから先頭の 3 文字を抽出します。

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="tolower" />
### toLower

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

<a id="toupper" />
### toUpper

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

<a id="trim" />
### trim

**trim (stringToTrim)**

指定された文字列から先頭と末尾の空白文字をすべて削除します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringToTrim | あり | トリムする文字列。

次の例では、ユーザー指定のパラメーター値から空白文字を削除します。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString, ...)**

パラメーターとして渡された値に基づいて一意の文字列を作成します。この関数は、リソースの一意の名前を作成する必要がある場合に便利です。結果の一意性のレベルを表すパラメーターの値を指定します。サブスクリプション、リソース グループ、またはデプロイメントに関して名前が一意であるかどうかを指定できます。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | あり | 一意の文字列を作成するためにハッシュ関数で使用される基本文字列。
| 必要に応じて追加のパラメーター | いいえ | 文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。

返される値はランダムな文字列ではなく、ハッシュ関数の結果になります。返される値は、13 文字です。グローバルに一意であるかは保証されません。命名規則にあるプレフィックスをこの値と組み合わせて、より認識しやすい名前を作成することもできます。

次の例は、uniqueString を使用して、一般的に使用されている別のレベル用に一意の値を作成する方法を示しています。

サブスクリプションに基づいて一意

    "[uniqueString(subscription().subscriptionId)]"

リソース グループに基づいて一意

    "[uniqueString(resourceGroup().id)]"

リソース グループのデプロイメントに基づいて一意

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
次の例は、リソース グループに基づいてストレージ アカウントの一意の名前を作成する方法を示しています。

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| baseUri | あり | ベース URI 文字列。
| relativeUri | あり | ベース URI 文字列に追加する相対 URI 文字列。

**baseUri** パラメーターの値には、特定のファイルを含めることができますが、URI の作成時には基本パスだけが使用されます。たとえば、**baseUri パラメーターとして http://contoso.com/resources/azuredeploy.json** を渡すと、http://contoso.com/resources/** というベース URI が作成されます**。

次の例は、親テンプレートの値に基づいて、入れ子になったテンプレートへのリンクを作成する方法を示しています。

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## 配列関数

リソース マネージャーには、配列値を操作する関数がいくつか用意されています。

- [concat](#concat)
- [length](#length)
- [skip](#skip)
- [split](#split)
- [take](#take)

<a id="length" />
### length

**length(配列または文字列)**

配列の要素数または文字列の文字数を返します。この関数を配列と共に使用して、リソースを作成するときのイテレーション数を指定できます。次の例では、**siteNames** パラメーターは、Web サイトの作成時に使用する名前の配列を参照します。

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

この関数を配列と共に使用する方法の詳細については、「[Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。

次のように、文字列と共に使用することもできます。

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }

<a id="skip" />
### skip
**skip(originalValue, numberToSkip)**

配列内の指定位置より後ろにあるすべての要素を含んだ配列または文字列内の指定位置より後ろにあるすべての文字を含んだ文字列を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| originalValue | あり | 要素または文字のスキップに使用する配列または文字列。
| numberToSkip | あり | スキップする要素数または文字数。この値が 0 以下である場合、配列または文字列のすべての要素が返されます。配列または文字列の長さを超える場合は、空の配列または空の文字列が返されます。 

指定した数の配列要素をスキップする例を次に示します。

    "parameters": {
      "first": {
        "type": "array",
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int"
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }

<a id="take" />
### take
**take(originalValue, numberToTake)**

開始位置を起点として指定された要素数の配列または開始位置を起点として指定された文字数の文字列を返します。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| originalValue | あり | 要素または文字の取得元となる配列または文字列。
| numberToTake | あり | 取得する要素数または文字数。この値が 0 以下である場合、空の配列または空の文字列が返されます。指定された配列または文字列の長さを超える場合、その配列または文字列のすべての要素が返されます。

指定した数の要素を配列から取得する例を次に示します。

    "parameters": {
      "first": {
        "type": "array",
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int"
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## デプロイの値関数

リソース マネージャーには、テンプレートのセクションから値を取得し、デプロイに関連する値を取得する次の関数が用意されています。

- [デプロイ](#deployment)
- [parameters](#parameters)
- [variables](#variables)

リソース、リソース グループ、サブスクリプションから値を取得する方法については、「[リソース関数](#resource-functions)」を参照してください。

<a id="deployment" />
### デプロイ

**デプロイ()**

現在のデプロイ操作に関する情報を返します。

この関数は、デプロイ中に渡されたオブジェクトを返します。返されるオブジェクトのプロパティは、デプロイ オブジェクトがリンクとして渡されたか、インライン オブジェクトとして渡されたかによって異なります。デプロイ オブジェクトがインラインで渡された場合 (Azure PowerShell の **TemplateFile** パラメーターを使用してローカル ファイルを参照する場合など)、返されるオブジェクトは次の形式になります。

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

オブジェクトがリンクとして渡された場合 (**TemplateUri** パラメーターを使用してリモート オブジェクトを参照する場合など)、オブジェクトは次の形式で返されます。

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

次の例は、deployment() を使用し、親テンプレートの URI に基づいて別のテンプレートにリンクする方法を示しています。

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

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

<a id="variables" />
### variables

**variables (variableName)**

変数の値を返します。指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| variable Name | あり | 返す変数の名前。



## リソース関数

リソース マネージャーには、リソース値を取得する次の関数が用意されています。

- [listkeys](#listkeys)
- [list*](#list)
- [providers](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [subscription](#subscription)

パラメーター、変数、現在のデプロイから値を取得する方法については、「[デプロイの値関数](#deployment-value-functions)」を参照してください。

<a id="listkeys" />
### listKeys

**listKeys (resourceName or resourceIdentifier, [apiVersion])**

listKeys 操作をサポートする任意の種類のリソースのキーを返します。resourceId は、[resourceId function](./#resourceid) を使用して、または形式 **providerNamespace/resourceType/resourceName** を使用して指定できます。関数を使用して、PrimaryKey と secondaryKey を取得できます。
  
| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| resourceName または resourceIdentifier | あり | リソースの一意識別子です。
| apiVersion | あり | リソースのランタイム状態の API バージョン。

次の例は、出力のセクションで、ストレージ アカウントからキーを返す方法を示しています。

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="list" />
### list*

**list* (resourceName または resourceIdentifier、apiVersion)**

**list** で始まるすべての操作はテンプレート内の関数として使用できます。これには、上で示すように **listKeys** が含まれ、また **list**、**listAdminKeys**、**listStatus** などの操作も含まれます。この関数を呼び出すとき、list* ではなく、関数の実際の名前を使用します。どのリソースの種類にリスト処理を含めるかを指定するには、次の PowerShell コマンドを使用します。

    PS C:\> Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

または、Azure CLI でリストを取得します。次の例では、**apiapps** のすべての操作を取得し、JSON ユーティリティ [jq](http://stedolan.github.io/jq/download/) を使用して、リスト処理のみをフィルタリングします。

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

<a id="providers" />
### プロバイダー

**providers (providerNamespace, [resourceType])**

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。種類が指定されない場合は、サポートされているすべての種類が返されます。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| providerNamespace | あり | プロバイダーの名前空間
| resourceType | いいえ | 指定した名前空間内にあるリソースの種類。

サポートされている種類は、それぞれ次の形式で返されます。ただし、配列の順序が維持されるとは限りません。

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

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

式が別のリソースのランタイム状態から値を取得できるようにします。

| パラメーター | 必須 | 説明
| :--------------------------------: | :------: | :----------
| resourceName または resourceIdentifier | あり | 名前またはリソースの一意の識別子。
| apiVersion | いいえ | 指定したリソースの API バージョンです。同じテンプレート内でリソースがプロビジョニングされないとき、このパラメーターを追加する必要があります。

**reference** 関数はその値をランタイム状態から取得するので、変数セクションでは使用できません。これは、テンプレートの出力セクションで使用できます。

reference 関数を使用して、参照先のリソースが同じテンプレート内でプロビジョニングされる場合に、あるリソースが他のリソースに依存することを暗黙的に宣言します。**dependsOn** プロパティを一緒に使用する必要はありません。参照先のリソースがデプロイを完了するまで、関数は評価されません。

次の例では、同じテンプレートでデプロイされるストレージ アカウントが参照されます。

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

次の例では、このテンプレートでデプロイされないが、デプロイされるリソースと同じリソース グループ内に存在するストレージ アカウントが参照されます。

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

下の図のように、BLOB エンドポイント URI など、返されたオブジェクトから特定の値を取得できます。

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

次の例では、別のリソース グループにあるストレージ アカウントが参照されます。

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### resourceGroup

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

<a id="resourceid" />
### resourceId

**resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

リソースの一意の識別子を返します。リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。識別子は、次の形式で返されます。

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| パラメーター | 必須 | 説明
| :---------------: | :------: | :----------
| subscriptionId | いいえ | 省略可能なサブスクリプション ID。既定値は、現在のサブスクリプションです。別のサブスクリプション内のリソースを取得するときには、この値を指定します。
| resourceGroupName | いいえ | 省略可能なリソース グループの名前。既定値は、現在のリソース グループです。別のリソース グループ内のリソースを取得するときには、この値を指定します。
| resourceType | あり | リソース プロバイダーの名前空間を含むリソースの種類。
| resourceName1 | あり | リソースの名前。
| resourceName2 | いいえ | リソースが入れ子になっている場合、次のリソース名セグメント。

次の例では、Web サイトとデータベースのリソース ID を取得する方法を示します。Web サイトは **myWebsitesGroup** という名前のリソース グループ内にあり、データベースはこのテンプレートの現在のリソース グループ内にあります。

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。ストレージ アカウントや仮想ネットワークは、複数のリソース グループ間で使用される場合があるので、単一のリソース グループを削除するときにそれらを削除しないでください。次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

<a id="subscription" />
### subscription

**subscription()**

サブスクリプションの詳細を次の形式で返します。

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

次の例は、出力セクションで呼び出される subscription 関数を示しています。

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## 次のステップ
- Azure リソース マネージャー テンプレートのセクションの説明については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」をご覧ください。
- 複数のテンプレートをマージするには、[Azure リソース マネージャーでのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページをご覧ください。
- 1 種類のリソースを指定した回数分繰り返し作成するには、「[Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。
- 作成したテンプレートをデプロイする方法を確認するには、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」をご覧ください。

<!---HONumber=AcomDC_0706_2016-->