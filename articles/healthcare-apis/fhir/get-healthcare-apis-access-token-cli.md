---
title: Azure CLI または Azure PowerShell を使用してアクセス トークンを取得する - FHIR サービス
description: この記事では、Azure CLI または Azure PowerShell を使用して FHIR サービスのアクセス トークンを取得する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 672089ac061430121916efff67280c08c1c6943f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619439"
---
# <a name="get-access-token-for-fhir-service-using-azure-cli-or-azure-powershell"></a>Azure CLI または Azure PowerShell を使用して FHIR サービスのアクセス トークンを取得する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この記事では、 Azure CLI を使用して、Azure Healthcare API (ここでは FHIR サービスと呼ばれる) で FHIR サービスのアクセス トークンを取得する方法について説明します。 [FHIR サービス をプロビジョニングする場合は](fhir-portal-quickstart.md)、サービスにアクセスできるユーザーまたはサービス プリンシパルのセットを構成します。 ユーザー オブジェクト ID が許可されているオブジェクト ID の一覧にある場合は、Azure CLI を使用して取得したトークンを使用して、サービスにアクセスすることができます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

この記事では、Azure Healthcare API (ここでは FHIR サービスと呼ばれる) で、Azure PowerShell を使用して FHIR サービスのアクセス トークンを取得する方法について説明します。 [FHIR サービス をプロビジョニングする場合は](fhir-portal-quickstart.md)、サービスにアクセスできるユーザーまたはサービス プリンシパルのセットを構成します。 ユーザー オブジェクト ID が許可されているオブジェクト ID の一覧にある場合は、 を使用して取得したトークンを使用してサービスAzure PowerShell。

[!INCLUDE [azure-powershell-requirements.md](../../../includes/azure-powershell-requirements.md)]

---
## <a name="obtain-a-token"></a>トークンを取得する

FHIR サービスは、FHIR サーバーの URI と等しい URI を持 `resource` `Audience` つ または を使用します `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-with-fhir-service"></a>FHIR サービスでの使用

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

---

>[!div class="nextstepaction"]
>[Postman を使用して FHIR API にアクセスする](../use-postman.md)
