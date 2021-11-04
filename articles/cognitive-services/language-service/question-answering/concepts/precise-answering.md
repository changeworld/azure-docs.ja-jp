---
title: 回答範囲検出を使用した正確な回答 - 質問応答
description: 質問応答で利用できる正確な回答機能について理解します。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 432e65d3d2f3dc3d4bc71157f165311e7fad87e9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476663"
---
# <a name="precise-answering"></a>正確な回答

導入された正確な回答機能を使用すると、ナレッジ ベースにある一番適した回答文章の候補から、ユーザーのすべてのクエリに対する正しい短い回答を得ることができます。 この機能では実行時にディープ ラーニング モデルを使用しています。これは、ユーザー クエリの意図を理解し、回答文章にファクトとして短い回答がある場合に、その短い回答を回答文章から検出します。

この機能は、コンテンツ開発者とエンドユーザーの両方に有益です。 コンテンツ開発者は、ナレッジベースにあるすべてのファクトに対して、特定の質問と回答のペアを手動でキュレートする必要がなくなりました。また、エンドユーザーは、サービスから返された回答全体を確認して、ユーザーのクエリの回答である実際のファクトを探す必要がなくなりました。

## <a name="precise-answering-via-the-portal"></a>ポータルを使用した正確な回答

[Language Studio ポータル](https://aka.ms/languageStudio)で、[テスト] ウィンドウを開くと、詳細オプションが表示されている上部に、 **[Include short answer response]\(短い回答を含める\)** オプションが表示されます。

[テスト] ウィンドウにクエリを入力すると、回答文章に短い回答がある場合、回答文章と共に短い回答が表示されます。

>[!div class="mx-imgBorder"]
>[![[テスト] ウィンドウのスクリーンショット。短い回答にチェック マークが入っており、質問に対して短い回答が表示されています。](../media/precise-answering/short-answer.png)](../media/precise-answering/short-answer.png#lightbox)

[テスト] ウィンドウに **長い回答** 文章のみを表示する場合は、 **[Include short answer response]\(短い回答を含める\)** オプションをオフにします。

このサービスでは、正確な回答の信頼度スコアも **回答範囲信頼度スコア** として返されます。これは、 **[検査]** オプションを選択して、選ばれた **詳細情報** で確認できます。

>[!div class="mx-imgBorder"]
>[![回答 - 範囲の信頼度スコアが表示されている検査ウィンドウのスクリーンショット。](../media/precise-answering/answer-confidence-score.png)](../media/precise-answering/answer-confidence-score.png#lightbox)

## <a name="deploying-a-bot"></a>ボットのデプロイ

ボットを発行すると、自分のアプリケーションで既定で正確な回答を、回答文章と共に短い回答を表示して提供できるようになります。 応答で正確な回答を使用する (AnswerSpan) 方法については、API リファレンスの「REST API」を参照してください。 Bot アプリ サービスを使用してテンプレートを更新すると、ユーザーは他のエクスペリエンスも柔軟に選択できるようになります。
