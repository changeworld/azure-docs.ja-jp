---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474166"
---
バインドの属性は、function.json ファイルで直接定義されています。 バインドの種類によっては、追加のプロパティが必要になることもあります。 [キュー出力構成](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration)では、Azure Storage キュー バインドに必要なフィールドについて説明されています。 この拡張機能により、バインドを簡単に function.json ファイルに追加できます。 

バインドを作成するには、HttpTrigger フォルダー内の `function.json` ファイルを右クリック (macOS では Ctrl キーを押しながらクリック) して、 **[バインドの追加]** を選択します。プロンプトに従って、新しいバインドの次のバインド プロパティを定義します。

| Prompt | Value | 説明 |
| -------- | ----- | ----------- |
| **Select binding direction (バインド方向を選択する)** | `out` | バインドは出力バインドです。 |
| **Select binding with direction... (方向を使用してバインドを選択する...)** | `Azure Queue Storage` | バインドは Azure Storage キュー バインドです。 |
| **コードでこのバインドの特定に使用する名前** | `msg` | コードで参照されているバインド パラメーターを識別する名前。 |
| **The queue to which the message will be sent (メッセージの送信先のキュー)** | `outqueue` | バインドが書き込むキューの名前。 *queueName* が存在しない場合は、バインドによって最初に使用されるときに作成されます。 |
| **Select setting from "local.setting.json" ("local.setting.json" から設定を選択する)** | `AzureWebJobsStorage` | ストレージ アカウントの接続文字列を含むアプリケーション設定の名前。 `AzureWebJobsStorage` 設定には、関数アプリで作成したストレージ アカウントの接続文字列が含まれています。 |

バインドは、function.json ファイルの `bindings` 配列に追加されます。このファイルは次の例のようになります。

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```