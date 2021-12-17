---
title: Azure CLI または Azure PowerShell を使用してアクセストークンを取得する
description: この記事では、Azure CLI または Azure PowerShell を使用して、医療 Api のアクセストークンを取得する方法について説明します。
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d350669abca87156cc5e735151f4a905d98745a8
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814656"
---
# <a name="get-access-token-using-azure-cli-or-azure-powershell"></a>Azure CLI または Azure PowerShell を使用してアクセストークンを取得する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、PowerShell と Azure CLI を使用して、FHIR サービスと DICOM サービスのアクセストークンを取得する方法について説明します。 医療 Api の使用を開始する方法の詳細については、「 [FHIR の使用を]() 開始する方法」または「 [DICOM の使用を開始する方法]()」を参照してください。 

---
## <a name="obtain-a-token-for-the-fhir-service"></a>FHIR サービスのトークンを取得する

FHIR サービスは、 `resource` `Audience` fhir サーバーの uri と同じ uri でまたはを使用し `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` ます。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-the-token-with-the-fhir-service"></a>FHIR サービスでトークンを使用する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

## <a name="obtain-a-token-for-the-dicom-service"></a>DICOM サービスのトークンを取得する

DICOM サービス では、DICOM サーバーの URI (`https://dicom.healthcareapis.azure.com`) と同じ URI で `resource` または `Audience` を使用します。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。


```Azure CLICopy
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-the-tokenb-with-the-dicom-service"></a>Tokenb と DICOM サービスを使用する

```Azure CLICopy
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>次の手順

- [Postman を使用して FHIR にアクセスする](use-postman.md)
- [FHIR にアクセスする Rest クライアント](using-rest-client.md)
- [CUrl を使用して DICOM にアクセスする](dicom/dicomweb-standard-apis-curl.md)

