---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262609"
---
## <a name="test"></a>関数をテストする

cURL を使用して、Mac または Linux コンピューターで、または Windows で Bash を使用して、デプロイした関数をテストします。 次の cURL コマンドを実行します。`<app_name>` プレースホルダーを Function App の名前に置き換えます。 URL にクエリ文字列 `&name=<yourname>` を追加します。

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

コマンド ラインで使用可能な cURL がない場合は、Web ブラウザーのアドレスに同じ URL を入力します。 再度、`<app_name>` プレースホルダーを関数アプリの名前に置き換え、URL にクエリ文字列 `&name=<yourname>` を追加して、要求を実行します。

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![ブラウザーに表示された関数の応答。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
