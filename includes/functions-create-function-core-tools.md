---
title: インクルード ファイル
description: インクルード ファイル
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 21b7226bc82aa8041db6bbd29b4874814e2a47d0
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279598"
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
