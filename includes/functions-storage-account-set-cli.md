---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839147"
---
local.settings.json ファイルを開き、ストレージ アカウントの接続文字列である `AzureWebJobsStorage` の値をコピーします。 次の Bash コマンドを使用して、`AZURE_STORAGE_CONNECTION_STRING` 環境変数に接続文字列を設定します。

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

`AZURE_STORAGE_CONNECTION_STRING` 環境変数に接続文字列を設定すると、毎回認証情報を指定しなくても、ストレージ アカウントにアクセスできます。