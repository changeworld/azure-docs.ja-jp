---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010496"
---
Azure で関数アプリのストリーミング ログを有効にするには:

1. F1 キーを選択してコマンド パレットを開き、コマンド **Azure Functions: [Start Streaming Logs]\(ログのストリーム配信を開始する\)** を検索して実行します。

1. Azure で関数アプリを選択してから、**[はい]** を選択して関数アプリのアプリケーションのログ記録を有効にします。

1. Azure で関数をトリガーします。 ログ データが Visual Studio Code の [出力] ウィンドウに表示されていることがわかります。

1. 完了したら、コマンド **Azure Functions: [ストリーミング ログの停止]** を実行して関数アプリのログ記録を無効にすることを忘れないでください。