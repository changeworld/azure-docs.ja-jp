---
title: Bicep 関数
description: 値の取得、文字列の処理、デプロイ情報の取得のために、Bicep ファイルで使用する関数について説明します。
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: dcc0e71917431ac2a181ae87374499a89a27482c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303987"
---
# <a name="bicep-functions"></a>Bicep 関数

この記事では、Bicep ファイルで使用できるすべての関数について説明します。 Bicep ファイルのセクションの説明については、「[Bicep ファイルの構造と構文について](./file.md)」を参照してください。

ほとんどの関数は、リソース グループ、サブスクリプション、管理グループ、またはテナントにデプロイされた場合に、同じように動作します。 いくつかの関数は、すべてのスコープでは使用できません。 これらは以下の一覧に記載されています。

## <a name="any-function"></a>すべての関数

Bicep では、データ型の警告に関する問題の解決に役立つ[任意の関数](./bicep-functions-any.md)を使用できます。

## <a name="array-functions"></a>配列関数

次の関数は、配列を操作するために使用できます。

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>データ関数

次の関数は、日付を操作するために使用できます。

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>デプロイの値関数

次の関数は、デプロイに関連する値を取得するために使用できます。

* [デプロイ](./bicep-functions-deployment.md#deployment)
* [environment](./bicep-functions-deployment.md#environment)

## <a name="logical-functions"></a>論理関数

次の関数は、論理条件を操作するために使用できます。

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>数値関数

次の関数は、整数を操作するために使用できます。

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>オブジェクト関数

次の関数は、オブジェクトを操作するために使用できます。

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>リソース関数

次の関数は、リソースの値を取得するために使用できます。

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

次の関数は、スコープの値を取得するために使用できます。

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - リソース グループへのデプロイでのみ使用できます。
* [subscription](./bicep-functions-scope.md#subscription) - リソース グループまたはサブスクリプションへのデプロイでのみ使用できます。
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>文字列関数

Bicep には、文字列を操作するための次の関数が用意されています。

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
* 1 種類のリソースを指定した回数分繰り返し作成するには、[Bicep でリソースの複数のインスタンスをデプロイする方法](./loop-resources.md)に関するページを参照してください。
* 作成した Bicep ファイルをデプロイする方法については、「[Bicep ファイルと Azure PowerShell を使用してリソースをデプロイする](./deploy-powershell.md)」を参照してください。
