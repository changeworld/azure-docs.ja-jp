---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170876"
---
## <a name="test"></a>Azure で関数をテストする

cURL を使用して、デプロイした関数をテストします。 前の手順からコピーした URL を関数キーも含めて使用して、URL にクエリ文字列 `&name=<yourname>` を追加します。

![cURL を使用した Azure の関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

コピーした URL を、関数キーも含めて、Web ブラウザーのアドレスに貼り付けることもできます。 繰り返しになりますが、要求を実行する前に、URL にクエリ文字列 `&name=<yourname>` を追加してください。

![Web ブラウザーを使用した関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
