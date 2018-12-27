---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452957"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Azure に関数アプリのプロジェクトをデプロイする

Azure で関数アプリを作成した後、[`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) コマンドを使用して Azure にプロジェクト コードをデプロイすることができます。

```bash
func azure functionapp publish <FunctionAppName>
```

読みやすさは不完全ですが、次のような出力が表示されます。

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

これで、Azure で関数をテストできます。