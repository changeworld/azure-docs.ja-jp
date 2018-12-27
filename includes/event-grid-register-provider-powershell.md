---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0946b703b45f69ede409167213f2629a796f18b5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869629"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする

Azure サブスクリプションで Event Grid を使用したことがない場合は、Event Grid リソース プロバイダーを登録する必要がある可能性があります。 次のコマンドを実行します。

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

登録完了まで少し時間がかかることがあります。 状態をチェックするには、次を実行します。

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

`RegistrationStatus` が `Registered` になったら、次に進めることができます。