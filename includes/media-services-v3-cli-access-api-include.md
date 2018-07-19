---
title: インクルード ファイル
description: インクルード ファイル
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: f4600413e05950446db71f988c4c4302f0dcacb3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39062989"
---
## <a name="access-the-media-services-api"></a>Media Services API にアクセスする

Azure Media Services API に接続するには、Azure AD サービス プリンシパル認証を使用します。 以下のコマンドでは、Azure AD アプリケーションを作成し、そのアカウントにサービス プリンシパルをアタッチしています。 返された値を使って、アプリケーションを構成する必要があります。

スクリプトを実行する前に、`amsaccount` と `amsResourceGroup` は、実際にリソースを作成するときに選んだ名前に置き換えてください。 `amsaccount` は、サービス プリンシパルのアタッチ先となる Azure Media Services アカウントの名前です。

次のコマンドは、`json` の出力を返します。

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

このコマンドからは、次のような応答が生成されます。

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

応答で `xml` を取得したい場合は、次のコマンドを使います。

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
