---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6c5ab187eb1307a95bbae4ff39d387221e094ea5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736871"
---
## <a name="enable-the-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする

Azure サブスクリプションで Event Grid を使用したことがない場合は、Event Grid リソース プロバイダーを登録する必要がある可能性があります。 以下のプロバイダーを登録するコマンドを実行します。

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

登録完了まで少し時間がかかることがあります。 状態をチェックするには、次を実行します。

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

`registrationState` が `Registered` になったら、次に進めることができます。
