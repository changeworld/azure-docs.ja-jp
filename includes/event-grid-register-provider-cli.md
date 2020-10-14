---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b48e5e28b15d17796138d493a7d9c628791df2e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "87830132"
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