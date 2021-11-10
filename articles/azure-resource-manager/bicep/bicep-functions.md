---
title: Bicep 関数
description: 値の取得、文字列の処理、デプロイ情報の取得のために、Bicep ファイルで使用する関数について説明します。
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 4a4f057fe7c29b3ee9b76aa03992ff1efd70079c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226912"
---
# <a name="bicep-functions"></a>Bicep 関数

この記事では、Bicep ファイルで使用できるすべての関数について説明します。 Bicep ファイルのセクションの説明については、「[Bicep ファイルの構造と構文について](./file.md)」を参照してください。

ほとんどの関数は、リソース グループ、サブスクリプション、管理グループ、またはテナントにデプロイされた場合に、同じように動作します。 いくつかの関数は、すべてのスコープでは使用できません。 これらは以下の一覧に記載されています。

## <a name="namespaces-for-functions"></a>関数の名前空間

Bicep 関数はすべて、`az` と `sys` の 2 つの名前空間内に含まれています。 通常、この関数を使用するときに名前空間を指定する必要はありません。 名前空間は、関数名が Bicep ファイルで定義されている別の項目と同じである場合にのみ指定します。 たとえば、`range` という名前のパラメーターを作成する場合は、`sys` 名前空間を追加して `range` 関数を区別する必要があります。

```bicep
// Parameter contains the same name as a function
param range int

// Must use sys namespace to call the function. 
// The second use of range refers to the parameter.
output result array = sys.range(1, range)
```

`az` 名前空間には、Azure のデプロイに固有の関数が含まれています。 `sys` 名前空間には、値を作成するために使用される関数が含まれています。 `sys` 名前空間にはまた、パラメーターとリソース ループのデコレーターも含まれています。 この記事では、これらの名前空間を記載しています。

## <a name="any-function"></a>すべての関数

Bicep では、データ型の警告に関する問題の解決に役立つ[任意の関数](./bicep-functions-any.md)を使用できます。 この関数は、`sys` 名前空間にあります。

## <a name="array-functions"></a>配列関数

次の関数は、配列を操作するために使用できます。 これらの関数はすべて、`sys` 名前空間にあります。

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [items](./bicep-functions-array.md#items)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>データ関数

次の関数は、日付を操作するために使用できます。 これらの関数はすべて、`sys` 名前空間にあります。

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>デプロイの値関数

次の関数は、デプロイに関連した値を取得するために使用できます。 これらの関数はすべて、`az` 名前空間にあります。

* [デプロイ](./bicep-functions-deployment.md#deployment)
* [environment](./bicep-functions-deployment.md#environment)

## <a name="file-functions"></a>ファイル関数

次の関数は、外部ファイルから Bicep ファイルにコンテンツを読み込むために使用できます。 これらの関数はすべて、`sys` 名前空間にあります。

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>論理関数

次の関数は、論理条件を操作するために使用できます。 この関数は、`sys` 名前空間にあります。

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>数値関数

次の関数は、整数を操作するために使用できます。 これらの関数はすべて、`sys` 名前空間にあります。

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>オブジェクト関数

次の関数は、オブジェクトを操作するために使用できます。 これらの関数はすべて、`sys` 名前空間にあります。

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>リソース関数

次の関数は、リソース値を取得するために使用できます。 これらの関数のほとんどは、`az` 名前空間にあります。 リスト関数と getSecret 関数は、リソースの種類で直接呼び出されるため、名前空間修飾子は含まれていません。

* [extensionResourceId](./bicep-functions-resource.md#extensionresourceid)
* [getSecret](./bicep-functions-resource.md#getsecret)
* [listAccountSas](./bicep-functions-resource.md#list)
* [listKeys](./bicep-functions-resource.md#listkeys)
* [listSecrets](./bicep-functions-resource.md#list)
* [list*](./bicep-functions-resource.md#list)
* [pickZones](./bicep-functions-resource.md#pickzones)
* [providers (非推奨)](./bicep-functions-resource.md#providers)
* [reference](./bicep-functions-resource.md#reference)
* [resourceId](./bicep-functions-resource.md#resourceid) - 任意のスコープで使用できますが、有効なパラメーターはスコープに応じて変わります。
* [subscriptionResourceId](./bicep-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](./bicep-functions-resource.md#tenantresourceid)

## <a name="scope-functions"></a>スコープ関数

次の関数は、スコープの値を取得するために使用できます。 これらの関数はすべて、`az` 名前空間にあります。

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - リソース グループへのデプロイでのみ使用できます。
* [subscription](./bicep-functions-scope.md#subscription) - リソース グループまたはサブスクリプションへのデプロイでのみ使用できます。
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>文字列関数

Bicep には、文字列を操作するための次の関数が用意されています。 これらの関数はすべて、`sys` 名前空間にあります。

* [base64](./bicep-functions-string.md#base64)
* [base64ToJson](./bicep-functions-string.md#base64tojson)
* [base64ToString](./bicep-functions-string.md#base64tostring)
* [concat](./bicep-functions-string.md#concat)
* [contains](./bicep-functions-string.md#contains)
* [dataUri](./bicep-functions-string.md#datauri)
* [dataUriToString](./bicep-functions-string.md#datauritostring)
* [empty](./bicep-functions-string.md#empty)
* [endsWith](./bicep-functions-string.md#endswith)
* [first](./bicep-functions-string.md#first)
* [format](./bicep-functions-string.md#format)
* [guid](./bicep-functions-string.md#guid)
* [indexOf](./bicep-functions-string.md#indexof)
* [last](./bicep-functions-string.md#last)
* [lastIndexOf](./bicep-functions-string.md#lastindexof)
* [length](./bicep-functions-string.md#length)
* [newGuid](./bicep-functions-string.md#newguid)
* [padLeft](./bicep-functions-string.md#padleft)
* [replace](./bicep-functions-string.md#replace)
* [skip](./bicep-functions-string.md#skip)
* [split](./bicep-functions-string.md#split)
* [startsWith](./bicep-functions-string.md#startswith)
* [string](./bicep-functions-string.md#string)
* [substring](./bicep-functions-string.md#substring)
* [take](./bicep-functions-string.md#take)
* [toLower](./bicep-functions-string.md#tolower)
* [toUpper](./bicep-functions-string.md#toupper)
* [trim](./bicep-functions-string.md#trim)
* [uniqueString](./bicep-functions-string.md#uniquestring)
* [uri](./bicep-functions-string.md#uri)
* [uriComponent](./bicep-functions-string.md#uricomponent)
* [uriComponentToString](./bicep-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>次のステップ

* Bicep ファイルのセクションの説明は、[Bicep ファイルの構造と構文](./file.md)に関する記事をご覧ください。
* リソースの種類を作成するときに指定した回数反復処理を行う場合は、[Bicep での反復ループ](loops.md)に関する記事を参照してください。
* 作成した Bicep ファイルをデプロイする方法については、「[Bicep ファイルと Azure PowerShell を使用してリソースをデプロイする](./deploy-powershell.md)」を参照してください。
