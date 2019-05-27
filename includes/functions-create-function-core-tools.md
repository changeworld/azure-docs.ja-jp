---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132133"
---
## <a name="create-a-function"></a>関数を作成する

次のコマンドは、HTTP でトリガーされる `MyHttpTrigger` という名前の関数を作成します。

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

コマンドを実行すると、次のような出力が表示されます。

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
