---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056554"
---
1. 関数を実行するには、Visual Studio で F5 キーを押します。 ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にすることが必要になる場合があります。 承認レベルは、ローカルで関数を実行するときには適用されません。

2. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![Azure ローカル ランタイム](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<YOUR_NAME>` を追加して、要求を実行します。 関数によって返されたローカルの GET 要求に対するブラウザーでの応答を次の図に示します。 

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. デバッグを停止するには、Visual Studio で Shift キーを押しながら F5 キーを押します。