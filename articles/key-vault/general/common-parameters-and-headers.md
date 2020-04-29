---
title: 一般的なパラメーターとヘッダー
description: Key Vault リソースに関連するすべての操作に共通のヘッダーおよびパラメーター。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427339"
---
# <a name="common-parameters-and-headers"></a>一般的なパラメーターとヘッダー

次の情報は、Key Vault リソースに関連するすべての操作に共通しています。

- `{api-version}` は、URI の api-version に置き換えます。
- `{subscription-id}` は、URI のサブスクリプション識別子に置き換えます
- `{resource-group-name}` は、リソース グループに置き換えます。 詳細については、リソース グループを使用した Azure リソースの管理に関するページを参照してください。
- `{vault-name}` は、URI の Key Vault 名に置き換えます。
- Content-Type ヘッダーは application/json に設定します。
- Azure Active Directory (AAD) から取得した Authorization ヘッダーを JSON Web トークンに設定します。 詳細については、「[Azure REST API Reference (Azure REST API リファレンス)](authentication-requests-and-responses.md)」を参照してください。

## <a name="common-error-response"></a>一般的なエラー応答
このサービスは、HTTP 状態コードを使用して成功または失敗を示します。 さらに、エラーには次の形式の応答が含まれています。

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|要素名 | 種類 | 説明 |
|---|---|---|
| コード | string | 発生したエラーの種類。|
| message | string | エラーの原因の説明。 |



## <a name="see-also"></a>参照
 [Azure Key Vault REST API リファレンス](/rest/api/keyvault/)
