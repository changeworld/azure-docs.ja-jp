---
title: 認証のための ID オブジェクト ID を見つける - Azure API for FHIR
description: この記事では、Azure API for FHIR の認証を構成するために必要な ID オブジェクト ID を見つける方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: b120b21a89a7105a25ba610402da99f9dce4b7e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194748"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>認証構成のための ID オブジェクト ID を見つける

この記事では、Azure API for FHIR の許可された ID オブジェクト ID の一覧を構成するために必要な ID オブジェクト ID を見つける方法を学習します。

フル マネージド Azure API for FHIR&reg; サービスは、ID オブジェクト ID の定義済み一覧へのアクセスのみを許可するように構成されます。 アプリケーションまたはユーザーが FHIR API にアクセスしようとする場合は、ベアラー トークンが提供される必要があります。 このベアラー トークンには、特定の要求 (フィールド) が含まれます。 FHIR API へのアクセス権を付与するには、このトークンに適切な発行者 (`iss`)、対象ユーザー (`aud`)、および許可されたオブジェクト ID の一覧のオブジェクト ID (`oid`) が含まれている必要があります。 ID オブジェクト ID は、ユーザーのオブジェクト ID または Azure Active Directory 内のサービス プリンシパルのどちらかです。

## <a name="configure-list-of-allowed-object-ids"></a>許可されたオブジェクト ID の一覧を構成する

新しい Azure API for FHIR インスタンスを作成する場合は、許可されたオブジェクト ID の一覧を構成できます。

![許可されたオブジェクト ID を構成する](media/quickstart-paas-portal/configure-allowed-oids.png)

これらのオブジェクト ID は、特定のユーザー ID または Azure Active Directory 内のサービス プリンシパルのどちらかにすることができます。

## <a name="find-user-object-id-using-powershell"></a>PowerShell を使用してユーザー オブジェクト ID を見つける

ユーザー名 `myuser@consoso.com` を持つユーザーがいる場合は、次の PowerShell コマンドを使用してユーザー `ObjectId` を見つけることができます。

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

または、次の Azure CLI を使用できます。

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id-using-powershell"></a>PowerShell を使用してサービス プリンシパル オブジェクト ID を見つける

[サービス クライアント アプリ](register-service-azure-ad-client-app.md)を登録しており、このサービス クライアントでの Azure API for FHIR へのアクセスを許可する場合は、次の PowerShell コマンドを使用してクライアント サービス プリンシパルのオブジェクト ID を見つけることができます。

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

ここで、`XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` はサービス クライアント アプリケーション ID です。 あるいは、そのサービス クライアントの `DisplayName` を使用できます。

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Azure CLI を使用している場合は、次を使用できます。

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="next-steps"></a>次の手順

この記事では、Azure API for FHIR インスタンスへのアクセスが許可された ID を構成するために必要な ID オブジェクト ID を見つける方法を学習しました。 次に、フル マネージド Azure API for FHIR をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Serverをデプロイする](fhir-paas-portal-quickstart.md)