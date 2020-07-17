---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279452"
---
## <a name="test"></a>Azure で関数を検証する

Web ブラウザーを使用して、デプロイした関数を検証できます。  関数キーも含めて URL をコピーし、Web ブラウザーのアドレス バーに貼り付けます。 要求を実行する前に、URL にクエリ文字列 `&name=<yourname>` を追加します。

![Web ブラウザーを使用した関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

また、cURL を使用して、デプロイした関数を検証することもできます。 前の手順からコピーした URL を関数キーも含めて使用して、URL にクエリ文字列 `&name=<yourname>` を追加します。

![cURL を使用した Azure の関数の呼び出し。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

