---
title: ストレージ アカウント名のエラー
description: Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイル内でストレージ アカウント名を指定するときに発生する可能性があるエラーについて説明します。
ms.topic: troubleshooting
ms.date: 11/12/2021
ms.openlocfilehash: 8f26efffac8768abb2279722fb1b5dbf174072f3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487751"
---
# <a name="resolve-errors-for-storage-account-names"></a>ストレージ アカウント名のエラーの解決

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルを使用してストレージ アカウントをデプロイするときに発生する可能性のある名前付けエラーについて説明します。

## <a name="symptom"></a>症状

ストレージ アカウント名に、大文字や感嘆符のような禁止文字が含まれる場合は、次のエラーが表示されます。

```Output
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

ストレージ アカウントの場合は、Azure 全体で一意のリソース名を指定する必要があります。 一意の名前を指定しない場合は、次のエラーが表示されます。

```Output
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

サブスクリプション内の既存のストレージ アカウントと同じ名前のストレージ アカウントを別の場所にデプロイすると、エラーが発生します。 このエラーは、ストレージ アカウントが別の場所に既に存在することを示します。 既存のストレージ アカウントを削除するか、既存のストレージ アカウントと同じ場所を使用します。

## <a name="cause"></a>原因

ストレージ アカウント名の長さは 3 から 24 文字で、数字と小文字のみを使用する必要があります。 大文字または特殊文字はありません。 名前は一意である必要があります。

## <a name="solution"></a>解決策

一意の名前を作成するには、使用している命名規則に、`uniqueString` 関数の結果を連結します。

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep は、[string interpolation](../bicep/bicep-functions-string.md#concat) を [uniqueString](../bicep/bicep-functions-string.md#uniquestring) と一緒に使用します。

```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

ARM テンプレートの場合は、[concat](../templates/template-functions-string.md#concat) を [uniqueString](../templates/template-functions-string.md#uniquestring) と一緒に使用します。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

---

ストレージ アカウント名が 24 文字を超えないようにしてください。 `uniqueString` 関数は、13 文字を返します。 プレフィックスまたはサフィックスを連結する場合は、11 文字以下の値を指定します。

次の例では、最大 11 文字のプレフィックスを作成するパラメーターを使用します。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('The prefix value for the storage account name.')
@maxLength(11)
param storageNamePrefix string = 'storage'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The prefix value for the storage account name."
    }
  }
}
```

---

次に、パラメーター値を `uniqueString` 値と連結して、ストレージ アカウント名を作成します。

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
name: '${storageNamePrefix}${uniqueString(resourceGroup().id)}'
```

# <a name="json"></a>[JSON](#tab/json)

```json
"name": "[concat(parameters('storageNamePrefix'), uniquestring(resourceGroup().id))]"
```

---
