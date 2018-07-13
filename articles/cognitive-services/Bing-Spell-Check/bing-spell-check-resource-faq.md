---
title: Bing Spell Check API についてよく寄せられる質問 - Azure Cognitive Services | Microsoft Docs
description: Azure 上の Bing Spell Check API についてよく寄せられる質問とその回答が掲載されています。
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377493"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Bing Spell Check API についてよく寄せられる質問

 Azure 上の Microsoft Cognitive Services の Bing Spell Check API に関連する概念、コード、シナリオについてよく寄せられる質問に対する回答を示します。

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>JavaScript から Bing Spell Check API を呼び出すときに、省略可能なクライアントのヘッダーを取得するにはどうすればよいですか?

次のヘッダーは省略可能ですが、必須として扱うことをお勧めします。 これらのヘッダーは、Bing Spell Check API がより正確な結果を返すことに役立ちます。

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

ただし、JavaScript から Bing Spell Check API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能によっては、これらのヘッダーの値にアクセスできない場合があります。

この問題を解決するため、CORS プロキシを介して Bing Spell Check API 要求を作成することができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録し、それらを JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーがあります。

CORS プロキシをインストールして[チュートリアル アプリ](tutorials/spellcheck.md)がオプションのクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 次に、コマンド プロンプトで次のコマンドを入力します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Spell Check API エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用しているときはコマンド ウィンドウを開いたままにしておきます。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、要求ごとに同じであることを確認できます。

## <a name="next-steps"></a>次の手順

不足している機能について質問がある場合は、 [UserVoice Web サイト](https://cognitive.uservoice.com/)でご要望またはご投票ください。

## <a name="see-also"></a>関連項目

 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
