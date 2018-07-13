---
title: FAQ - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: FAQ
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376336"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>指定した URL/ファイルから質問とその回答のペアが抽出されません。なぜでしょうか?

QnA Maker は、有効な FAQ URL であっても、一部の Q&A (QnA) コンテンツを自動抽出できない場合があります。 そのような場合は、QnA コンテンツを .txt ファイルに貼り付けて、ツールによる取り込みが可能かどうかを確認できます。 ナレッジ ベースに編集という形でコンテンツを追加することもできます。

## <a name="how-large-a-knowledge-base-can-i-create"></a>作成できるナレッジ ベースの最大サイズはどれくらいですか。

ナレッジ ベースのサイズは、QnA Maker サービスの作成時に選択した Azure Search の SKU により異なります。 詳細については、[こちら](./Tutorials/choosing-capacity-qnamaker-deployment.md)を参照してください。

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>新しいナレッジ ベースを作成しようとしているのですが、ドロップダウンに選択肢が表示されません。なぜでしょうか?

まだ Azure に QnA Maker サービスが作成されていません。 その方法については、[こちら](./How-To/set-up-qnamaker-service-azure.md)を参照してください。

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>複数人でナレッジ ベースを共有するにはどうすればよいですか?

共有は、QnA Maker サービス レベルで行われます。つまり、サービス内のすべてのナレッジ ベースが共有されます。 ナレッジ ベースの共同作業の方法については、[こちら](./How-To/collaborate-knowledge-base.md)を参照してください。

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>適切な一致が見つからなかったときの既定のメッセージを変更するにはどうすればよいですか?

既定のメッセージは、App Service の設定に含まれています。
- Azure portal の App Service リソースに移動します。

![QnA Maker App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **[設定]** オプションをクリックします。

![QnA Maker App Service の設定](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **[DefaultAnswer]** という設定の値を変更します。
- App Service を再起動します。

![QnA Maker App Service の再起動](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>SharePoint リンクが抽出されません。なぜでしょうか?

このツールで解析されるのはパブリック URL だけです。認証を伴うデータ ソースは、現時点ではサポートされません。 ファイルをダウンロードし、ファイルのアップロード オプションを使用して、質問とその回答を抽出することもできます。


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>ナレッジ ベースに対して行った更新が発行時に反映されません。 なぜでしょうか?

編集内容を公開するには、テーブルの更新、テスト、設定のいずれであってもすべて保存する必要があります。 編集操作後は、必ず [Save and train]\(保存してトレーニング\) ボタンをクリックしてください。

## <a name="when-should-i-refresh-my-endpoint-keys"></a>エンドポイント キーは、どのようなときに更新すべきでしょうか?

エンドポイント キーの更新は、鍵漏えいのおそれがある場合に行ってください。

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>ナレッジ ベースは、リッチ データやマルチメディアに対応していますか?

ナレッジ ベースは Markdown に対応しています。 ただし、URL からの自動抽出では、HTML から Markdown への変換機能が制限されています。 Markdown の機能をすべて使用するには、テーブル内のコンテンツを直接編集するか、リッチ コンテンツと共にナレッジ ベースをアップロードしてください。

画像やビデオなどのマルチメディアには、現時点では対応していません。

## <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker では英語以外の言語もサポートされますか。

詳細については、[サポートされている言語](./Overview/languages-supported.md)に関するページを参照してください。

多言語コンテンツが含まれる場合は、言語ごとにサービスを作成してください。

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>QnA Maker を利用するには Bot Framework を使う必要がありますか?

いいえ。QnA Maker に関して Bot Framework を使用する必要はありません。 ただし、QnA Maker は Azure Bot Service に用意されているいくつかのテンプレートの 1 つとして提供されています。 Bot Service では、Microsoft Bot Framework を使用してインテリジェントなボットを迅速に開発し、サーバーレス環境で実行することができます。

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>QnA Maker でボットを作成するにはどうすればよいですか?

Azure Bot でボットを作成するには、[こちら](./Tutorials/create-qna-bot.md)のドキュメントの手順に従ってください。

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>QnA Maker サービスを Web サイトに埋め込むにはどうすればよいですか?

Web サイトに QnA Maker サービスを Web チャット コントロールとして埋め込むには、次の手順に従ってください。

1. [こちら](./Tutorials/create-qna-bot.md)の手順に従って、FAQ ボットを作成します。
2. [こちら](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat)の手順に従って、Web チャットを有効にします。


