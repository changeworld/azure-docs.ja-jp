---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "68669706"
---
Azure で関数アプリのストリーミング ログを有効にするには:

1. F1 キーを選択してコマンド パレットを開き、次のコマンドを検索して実行します: **Azure Functions: Start Streaming Logs**。

1. Azure で関数アプリを選択してから、 **[はい]** を選択して関数アプリのアプリケーションのログ記録を有効にします。

1. Azure で関数をトリガーします。 ログ データが Visual Studio Code の [出力] ウィンドウに表示されていることがわかります。

1. 完了したら、忘れずにコマンド **Azure Functions: Stop Streaming Logs** を実行して、関数アプリのログを無効にしてください。