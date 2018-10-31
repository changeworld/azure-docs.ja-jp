---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987998"
---
## <a name="update-the-function"></a>関数を更新する

既定では、テンプレートによって、要求時に関数キーを要求する関数が作成されます。 Azure で関数をテストしやすくするために、匿名アクセスを許可するように関数を更新する必要があります。 この変更を行う方法は、関数プロジェクトの言語によって異なります。

### <a name="c"></a>C\#

新しい関数である MyHttpTrigger.cs コード ファイルを開き、関数定義の中の **AuthorizationLevel** 属性の値を `anonymous` に更新し、変更を保存します。

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

新しい関数である function.json ファイルをテキスト エディターで開き、**bindings.httpTrigger** 内の **authLevel** プロパティ を `anonymous` に更新し、変更を保存します。

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
