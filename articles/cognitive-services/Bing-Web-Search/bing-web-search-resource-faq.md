---
title: Azure 上の Bing Web Search API についてよく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure 上の Microsoft Cognitive Services Bing Web Search API についてよく寄せられる質問に対する回答を確認します。
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373125"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>Bing Web Search API (Cognitive Services) についてよく寄せられる質問 (FAQ)
 
 Azure 上の Microsoft Cognitive Services の Bing Web Search API に関連する概念、コード、シナリオについてよく寄せられる質問に対する回答を示します。

## <a name="response-headers-in-javascript"></a>JavaScript 内の応答ヘッダー

次のヘッダーは、Bing Web Search API からの応答に含まれる可能性があります。

|||
|-|-|
|`X-MSEdge-ClientID`|Bing がユーザーに割り当てた一意の ID|
|`BingAPIs-Market`|要求を満たすために使用された市場|
|`BingAPIs-TraceId`|この要求に対する Bing API サーバー上のログ エントリ (サポート用)|

クライアント ID を保持し、後続の要求で返すことが特に重要です。 これを行うときに、検索は検索結果の順位付けに過去のコンテキストを使用し、一貫性のあるユーザー エクスペリエンスも提供します。

ただし、JavaScript から Bing Web Search API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能 (CORS) によっては、これらのヘッダーの値にアクセスできないことがあります。

ヘッダーにアクセスするために、CORS プロキシを介して Bing Web Search API 要求を行うことができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録して JavaScript で使用可能にする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして[チュートリアル アプリ](tutorial-bing-web-search-single-page-app.md)がオプションのクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 次に、コマンド プロンプトで次のコマンドを入力します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Web Search API エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用しているときはコマンド ウィンドウを開いたままにしておきます。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、要求ごとに同じであることを確認できます。

## <a name="response-headers-in-production"></a>実稼働環境での応答ヘッダー

前の回答で説明されている CORS プロキシ アプローチは、開発、テスト、学習に適しています。 

一方、実稼働環境では、Bing Web Search API を使用する Web ページと同じドメイン上のサーバー側スクリプトをホストする必要があります。 このスクリプトは、実際には Web ページの JavaScript からの要求時に API 呼び出しを実行し、ヘッダーを含むすべての結果をクライアントに戻します。 2 つのリソース (ページとスクリプト) が配信元を共有するので、CORS は関与せず、特殊なヘッダーが Web ページ上の JavaScript からアクセスできます。 

API キーはサーバー側スクリプトでのみ必要なので、このアプローチでは API キーもパブリックへの露出から保護されます。 スクリプトでは、別の方法を使用して、要求が承認されているかどうかを確認できます。

## <a name="next-steps"></a>次の手順

不足している機能について質問がある場合は、 [UserVoice Web サイト](https://cognitive.uservoice.com/forums/555907-bing-search)でご要望またはご投票ください。

## <a name="see-also"></a>関連項目

 [Stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)