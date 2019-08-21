---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949979"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Azure に関数アプリのプロジェクトをデプロイする

Azure で関数アプリを作成した後、[`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools コマンドを使用して Azure にプロジェクト コードをデプロイすることができます。 以下の例の `<APP_NAME>` は、前の手順で作成したアプリの名前に置き換えてください。

### <a name="c--javascript"></a>C\# または JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

次のような出力が表示されます (読みやすくするために一部が省略されています)。

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

`HttpTrigger` の `Invoke url` 値をコピーします。これを使用して Azure で関数をテストできるようになります。 URL には、関数キーである `code` クエリ文字列が含まれています。 このキーにより、他のユーザーが Azure で HTTP トリガー エンドポイントを呼び出すことが難しくなります。