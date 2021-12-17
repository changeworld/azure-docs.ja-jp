---
title: Bicep 関数 - ファイル
description: ファイルから内容を読み込むために Bicep ファイルで使用する関数について説明します。
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 28b28086986eb4e871cc6ed8c315c3802e721840
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359838"
---
# <a name="file-functions-for-bicep"></a>Bicep のファイル関数

この記事では、外部ファイルから内容を読み込むための Bicep 関数について説明します。

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

ファイルを base64 文字列として読み込みます。

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| filePath | はい | string | 読み込むファイルのパス。 パスはデプロイされた Bicep ファイルからの相対パスです。 |

### <a name="remarks"></a>注釈

この関数は、デプロイに含めるバイナリ コンテンツがある場合に使用します。 ファイルを手動で base64 文字列にエンコードして Bicep ファイルに追加するのではなく、この関数を使用してファイルを読み込みます。 このファイルは、Bicep ファイルが JSON テンプレートにコンパイルされるときに読み込まれます。 デプロイ時に、JSON テンプレートには、ファイルの内容がハードコーディングされた文字列として含まれます。

この関数は、**Bicep バージョン 0.4.412 以降** が必要です。 

ファイルの最大許容サイズは **96 KB** です。

### <a name="return-value"></a>戻り値

base64 文字列形式のファイル。

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

指定したファイルの内容を文字列として読み込みます。 

名前空間: [sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| filePath | はい | string | 読み込むファイルのパス。 パスはデプロイされた Bicep ファイルからの相対パスです。 |
| encoding | いいえ | string | ファイルのエンコード。 既定値は <ph id="ph1">`utf-8`</ph> です。 使用できるオプションは、`iso-8859-1`、`us-ascii`、`utf-16`、`utf-16BE`、または `utf-8` です。  |

### <a name="remarks"></a>注釈

この関数は、より多くの内容を別のファイルに格納する場合に使用します。 Bicep ファイルに内容を複製するのではなく、この関数を使用して内容を読み込みます。 たとえば、デプロイ スクリプトをファイルから読み込むことができます。 このファイルは、Bicep ファイルが JSON テンプレートにコンパイルされるときに読み込まれます。 デプロイ時に、JSON テンプレートには、ファイルの内容がハードコーディングされた文字列として含まれます。

JSON ファイルを読み込むときは、[json](bicep-functions-object.md#json) 関数を loadTextContent 関数と共に使用して JSON オブジェクトを作成することができます。 VS Code では、読み込まれたオブジェクトのプロパティを intellisense で使用できます。 たとえば、多数の Bicep ファイルで共有する値を持つファイルを作成できます。 この記事ではその例を紹介しています。

この関数は、**Bicep バージョン 0.4.412 以降** が必要です。

ファイルの最大許容サイズは、行末を含めて **131,072 文字** です。

### <a name="return-value"></a>戻り値

文字列形式のファイルの内容。

### <a name="examples"></a>例

次の例では、ファイルからスクリプトを読み込み、それをデプロイ スクリプトに使用します。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

次の例では、ネットワーク セキュリティ グループに使用する値を含む JSON ファイルを作成します。

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

そのファイルを読み込み、JSON オブジェクトに変換します。 そのオブジェクトを使用して、リソースに値を割り当てます。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

ネットワーク セキュリティ グループをデプロイする他の Bicep ファイルで、値のファイルを再利用できます。

## <a name="next-steps"></a>次のステップ

* Bicep ファイルのセクションの説明は、<bpt id="p1">[</bpt>Bicep ファイルの構造と構文<ept id="p1">](./file.md)</ept>に関する記事をご覧ください。
