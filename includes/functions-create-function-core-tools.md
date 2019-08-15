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
ms.openlocfilehash: 83f3def8f4972f35df25dc98f4b818e703801d2e
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950001"
---
## <a name="create-a-function"></a>関数を作成する

次のコマンドは、HTTP でトリガーされる `MyHttpTrigger` という名前の関数を作成します。

```command
func new --name MyHttpTrigger --template "HttpTrigger"
```

コマンドを実行すると、次のような出力が表示されます。

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
