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
ms.openlocfilehash: 74d96952d6cafb6bd7bb16ccf2f4d69b4fbd6de3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40233967"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする

Azure サブスクリプションで Event Grid を使用したことがない場合は、Event Grid リソース プロバイダーを登録する必要がある可能性があります。 以下のプロバイダーを登録するコマンドを実行します。

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

登録完了まで少し時間がかかることがあります。 状態をチェックするには、次を実行します。

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

`registrationState` が `Registered` になったら、次に進めることができます。