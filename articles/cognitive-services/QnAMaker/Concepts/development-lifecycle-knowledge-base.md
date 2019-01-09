---
title: ナレッジ ベースのライフサイクル - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker は、モデル変更、音声例、公開、エンドポイント クエリからのデータ収集の最適な反復サイクルを学習します。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 68fcc8b324bebfc7ca4fa94b2633b475137ea580
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075596"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker におけるナレッジ ベースのライフサイクル
QnA Maker は、モデル変更、音声例、公開、エンドポイント クエリからのデータ収集の最適な反復サイクルを学習します。 

![作成サイクル](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成
QnA Maker ナレッジ ベース (KB) エンドポイントでは、KB のコンテンツに基づいて、ユーザー クエリに対する最も一致する回答を提供します。 ナレッジ ベースの作成は、質問、回答、関連付けられているメタデータのコンテンツ リポジトリを設定する場合に行う 1 回限りの操作です。 ナレッジ ベースは、FAQ ページ、製品マニュアル、構造化された Q と A のペアなど、既存のコンテンツをクロールすることで作成できます。 [ナレッジ ベースの作成](../How-To/create-knowledge-base.md)方法を確認してください。

## <a name="testing-and-updating-the-knowledge-base"></a>ナレッジ ベースのテストと更新
編集または自動抽出を行ってナレッジ ベースにコンテンツを取り込んだら、テストすることができます。 テストは **[テスト]** パネルから行うことができます。その場合、一般的なユーザー クエリを入力し、返された応答が予期したものであり、その信頼度スコアが十分であることを確認します。 低い信頼度スコアを修正するために代わりの質問を追加できます。 また、クエリで "KB に一致するものが見つかりません" という既定の応答が返された場合、新しい回答を追加することもできます。 結果に満足するまで、このテストと更新の短いループが続きます。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。

大規模な KB の場合は、generateAnswer API を使用して、テストを自動化することができます。 

## <a name="publish-the-knowledge-base"></a>ナレッジ ベースの公開
ナレッジ ベースのテストが完了したら、それを公開することができます。 公開時に、テスト済みのナレッジ ベースの最新バージョンが、**公開済み**のナレッジ ベースを表す専用の Azure Search インデックスにプッシュされます。 また、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

この方法では、ナレッジ ベースのテスト バージョンに加えられた変更は、実稼働アプリケーションに存在する可能性のある公開済みのバージョンには影響しません。

これらのナレッジ ベースをそれぞれ別個にテスト対象とすることができます。 API を使用して、generateAnswer 呼び出しで `isTest=true` フラグを指定し、ナレッジ ベースのテスト バージョンを対象とすることができます。

[ナレッジ ベースの公開](../How-To/publish-knowledge-base.md)方法を確認してください。

## <a name="monitor-usage"></a>使用状況の監視
サービスのチャット ログを記録できるようにするには、[QnA Maker サービスを作成する](../How-To/set-up-qnamaker-service-azure.md)際に Application Insights を有効にする必要があります。

サービスの使用状況について、さまざまな分析を行うことができます。 Application Insights を使用して [QnA Maker サービスを分析する](../How-To/get-analytics-knowledge-base.md)方法の詳細を確認してください。

分析から学んだ内容に基づいて、適切な[ナレッジ ベースの更新](../How-To/edit-knowledge-base.md)を行います。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [信頼度スコア](./confidence-score.md)

## <a name="see-also"></a>関連項目 

[ナレッジ ベース](./knowledge-base.md)
[QnA Maker の概要](../Overview/overview.md)
