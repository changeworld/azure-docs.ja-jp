---
title: Azure Cloud Shell でのユーザー トークンの取得
description: Azure Cloud Shell で認証済みユーザーのトークンを取得する方法
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/29/2021
ms.openlocfilehash: 117fa3672c78de29cd88797add83fa6e3bf2bf79
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362741"
---
# <a name="acquire-a-token-in-azure-cloud-shell"></a>Azure Cloud Shell でトークンを取得する

Azure Cloud Shell には、Azure portal にログインしたユーザーを自動的に認証するエンドポイントが用意されています。 このエンドポイントを使用して、Azure サービスと対話するためのアクセス トークンを取得します。

## <a name="authenticating-in-the-cloud-shell"></a>Cloud Shell での認証
Azure Cloud Shell には、ブラウザーと対話してユーザーを自動的にログインさせる独自のエンドポイントがあります。 このエンドポイント側で要求が受信されると、ブラウザーに要求が送り返され、そこから親のポータル フレームに転送されます。 ポータル ウィンドウから Azure Active Directory に対して要求が行われ、結果としてトークンが返されます。

異なる資格情報を使用して認証したい場合は、`az login` または `Connect-AzAccount` を使用して認証できます。

## <a name="acquire-and-use-access-token-in-cloud-shell"></a>Cloud Shell でアクセス トークンを取得して使用する

### <a name="acquire-token"></a>トークンを取得する

次のコマンドを実行し、環境変数 `access_token` にユーザー アクセス トークンを設定します。
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The access token is $access_token
```

### <a name="use-token"></a>トークンを使用する

次のコマンドを実行し、前の手順で取得したトークンを使用して、アカウント内のすべての仮想マシン一覧を取得します。

```
curl https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Compute/virtualMachines?api-version=2021-07-01 -H "Authorization: Bearer $access_token" -H "x-ms-version: 2019-02-02"
```

## <a name="handling-token-expiration"></a>トークンの有効期限の処理

トークンはローカル認証エンドポイントによってキャッシュされます。 これは何度でも呼び出すことができます。また、Azure Active Directory に対する認証呼び出しは、キャッシュにトークンが格納されていないか、トークンが期限切れの場合にのみ発生します。

## <a name="limitations"></a>制限事項
- Cloud Shell トークンを提供できるリソースの許可リストがあります。 コマンドを実行して `"error":{"code":"AudienceNotSupported","message":"Audience https://newservice.azure.com/ is not a supported MSI token audience...."}` のようなメッセージが表示された場合は、この制限にぶつかったことを意味します。 [GitHub](https://github.com/Azure/CloudShell/issues) にイシューを提出して、このサービスを許可リストに追加するように依頼することができます。
- `az login` コマンドを使用して明示的にログインした場合、会社が設定しているすべての条件付きアクセスの規則は、ブラウザーが実行されているマシンではなく、Cloud Shell コンテナーに基づいて評価されます。 Cloud Shell コンテナーはこれらのポリシーのマネージド デバイスとしてカウントされないため、ポリシーによって権限が制限される可能性があります。
- Azure マネージド ID は、Azure Cloud Shell では使用できません。 [Azure マネージド ID の詳細について参照してください](../active-directory/managed-identities-azure-resources/overview.md)。
