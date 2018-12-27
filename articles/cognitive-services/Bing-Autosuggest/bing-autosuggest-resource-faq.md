---
title: よく寄せられる質問 (FAQ) - Bing Autosuggest API
titlesuffix: Azure Cognitive Services
description: Bing Autosuggest API についてよく寄せられる質問とその回答が掲載されています。
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 84f1b0555922119e9de4addc3d51ac233e7bae65
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831366"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Bing Autosuggest API についてよく寄せられる質問 (FAQ)
 
 Azure Cognitive Services の Autosuggest API に関連する概念、コード、およびシナリオについてよく寄せられる質問に対する回答を示します。

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>JavaScript から Bing Autosuggest API を呼び出すときに、省略可能なクライアントのヘッダーを取得するにはどうすればよいですか?

次のヘッダーは省略可能ですが、必須として扱うことをお勧めします。 これらのヘッダーは、Bing Autosuggest API がより正確な結果を返すことに役立ちます。

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

ただし、JavaScript から Bing Autosuggest API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能によっては、これらのヘッダーの値にアクセスできない場合があります。

これを解決するため、CORS プロキシを介して Bing Autosuggest API 要求を作成することができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録し、それらを JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーがあります。

CORS プロキシをインストールして[チュートリアル アプリ](tutorials/autosuggest.md)が省略可能なクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 その後、コマンド プロンプトで次のコマンドを入力します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Autosuggest API エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

## <a name="next-steps"></a>次の手順

不足している機能について質問がある場合は、 [ユーザーの声 Web サイト](https://cognitive.uservoice.com/)で、要求またはそれに対する投票をご検討ください。

## <a name="see-also"></a>関連項目

- [Stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
