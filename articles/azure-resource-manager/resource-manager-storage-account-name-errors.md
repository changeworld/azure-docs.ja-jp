---
title: "Azure ストレージ アカウント名のエラー | Microsoft Docs"
description: "ストレージ アカウント名を指定する際に発生する可能性のあるエラーについて説明します。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: dc045827fbd38054a334ff22eb30e0db6a31bac8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-storage-account-names"></a>ストレージ アカウント名のエラーの解決

この記事では、ストレージ アカウントをデプロイする際に発生する可能性のある名前付けに関するエラーについて説明します。

## <a name="symptom"></a>症状

ストレージ アカウント名に禁止文字が含まれる場合、次のようなエラーが表示されます。

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

ストレージ アカウントには、Azure 全体で一意であるリソースの名前を指定する必要があります。 一意の名前を指定しないと、次のようなエラーが発生します。

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

ストレージ アカウントをデプロイするとき、ご利用のサブスクリプションに既に存在するストレージ アカウントと同じ名前を使用したうえで別の場所にデプロイすると、ストレージ アカウントが既に別の場所に存在しているというエラーが表示されます。 既存のストレージ アカウントを削除するか、既存のストレージ アカウントと同じ場所を指定してください。

## <a name="cause"></a>原因

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。 名前は一意である必要があります。

## <a name="solution"></a>解決策

### <a name="solution-1"></a>解決策 1

ストレージ アカウント名は必ず一意にしてください。 一意の名前を作成するには、使用している命名規則に、 [uniqueString](resource-group-template-functions-string.md#uniquestring) 関数の結果を連結します。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

### <a name="solution-2"></a>解決策 2

ストレージ アカウント名が 24 文字を超えないようにしてください。 [uniqueString](resource-group-template-functions-string.md#uniquestring) 関数は 13 文字を返します。 **uniqueString** の結果にプレフィックスまたはポストフィックスを連結する場合は、11 文字以下の値を指定してください。

```json
"parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
}
```

### <a name="solution-3"></a>解決策 3

ストレージ アカウント名には、大文字や特殊文字を含めないでください。