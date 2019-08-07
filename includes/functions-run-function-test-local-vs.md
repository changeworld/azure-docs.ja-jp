---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592864"
---
1. 関数を実行するには、**F5** キーを押します。 ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にする必要がある場合もあります。 承認レベルは、ローカルでの実行時には適用されません。

2. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![Azure ローカル ランタイム](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 `?name=<YOUR_NAME>` を追加して、要求を実行します。 関数によって返されたローカルの GET 要求に対するブラウザーでの応答を次に示します。 

    ![ブラウザーでの関数 localhost の応答](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. デバッグを停止するには、**Shift キーを押しながら F5 キー**を押します。