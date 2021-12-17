---
title: Azure CLI を使用してアクセス トークンを取得する - Azure API for FHIR
description: この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: b955fd521224197a3505bd24f7ff0d02ec95fb5d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748941"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する

この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法について説明します。 [Azure API for FHIR をプロビジョニングする](fhir-paas-portal-quickstart.md)場合は、サービスにアクセスできるユーザーまたはサービス プリンシパルのセットを構成します。 ユーザー オブジェクト ID が許可されているオブジェクト ID の一覧にある場合は、Azure CLI を使用して取得したトークンを使用して、サービスにアクセスすることができます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>トークンを取得する

Azure API for FHIR は、FHIR サーバーの URI (`https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com`) と同じ URI で `resource` または `Audience` を使用します。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。

```azurecli-interactive
$token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Azure API for FHIR での使用

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure CLI を使用して Azure API for FHIR のアクセス トークンを取得する方法を学習しました。 Postman を使用して FHIR API にアクセスする方法を学習するには、Postman のチュートリアルに進んでください。

>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](./../use-postman.md)
