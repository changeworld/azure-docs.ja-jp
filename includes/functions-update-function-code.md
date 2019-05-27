---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132180"
---
## <a name="update-the-function"></a>関数を更新する

既定では、テンプレートによって、要求時に関数キーを要求する関数が作成されます。 Azure で関数をテストしやすくするために、匿名アクセスを許可するように関数を更新する必要があります。 この変更を行う方法は、関数プロジェクトの言語によって異なります。

### <a name="c"></a>C\#

新しい関数である MyHttpTrigger.cs コード ファイルを開き、関数定義の中の **AuthorizationLevel** 属性の値を `Anonymous` に更新し、変更を保存します。

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

新しい関数の function.json ファイルをテキスト エディターで開き、**bindings** 内の **authLevel** プロパティを `anonymous` に更新し、変更を保存します。

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

これで、関数キーを指定することなく Azure で関数を呼び出すことができます。 ローカルでの実行時に関数キーが要求されることはありません。
