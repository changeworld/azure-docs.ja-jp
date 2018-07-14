---
title: Azure Autosuggest API についてよく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure での Azure Cognitive Services の Autosuggest API に関する一般的な質問の回答をご覧いただけます。
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376861"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Autosuggest API (Cognitive Services) についてよく寄せられる質問 (FAQ)
 
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
