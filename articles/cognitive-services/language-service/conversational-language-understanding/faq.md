---
title: よく寄せられる質問
titleSuffix: Azure Cognitive Services
description: この記事では、会話言語理解に関する FAQ への回答を簡単に説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e699beb4bae6c31c73e2e5eebc508306deeb262
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091315"
---
# <a name="frequently-asked-questions-for-conversational-language-understanding"></a>会話言語理解に関してよくあるご質問

この記事では、会話言語理解に関してよくあるご質問への回答を簡単に説明します。

## <a name="how-do-i-create-a-project"></a>プロジェクトを作成するにはどうすればよいですか?

初めてのプロジェクトを手軽に作るには、[クイックスタート](./quickstart.md)に関する記事をご覧ください。詳しくは、[方法に関する記事](./how-to/create-project.md)をご覧ください。 

## <a name="how-do-i-connect-other-service-applications-in-orchestration-workflow-projects"></a>オーケストレーション ワークフロー プロジェクトで他のサービス アプリケーションに接続するにはどうすればよいですか?

意図として別のプロジェクトを接続する方法については、[スキーマの作成に関する記事](./how-to/build-schema.md#build-project-schema-for-orchestration-workflow-projects)をご覧ください。

## <a name="which-luis-applications-can-i-connect-to-in-orchestration-workflow-projects"></a>オーケストレーション ワークフロー プロジェクトでは、どの LUIS アプリケーションに接続できますか?

言語リソースを作成リソースとして使う LUIS アプリケーションは、接続に利用できます。 同じリソースによって所有される LUIS アプリケーションにのみ接続できます。 LUIS と CLU の両方を利用できるリージョンは西ヨーロッパだけなので、このオプションは西ヨーロッパでのみ利用できます。 詳しくは、[リージョンの制限](./service-limits.md#region-limits)に関する記事をご覧ください。 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>トレーニングに長い時間がかかりますが、これは予想されることですか?

会話プロジェクトの場合は、長いトレーニング時間が予想されます。 使う例の数により、トレーニング時間は 5 分から 1 時間以上になる場合があります。 

## <a name="can-i-add-entities-to-orchestration-workflow-projects"></a>オーケストレーション ワークフロー プロジェクトにエンティティを追加できますか?

不正解です。 オーケストレーション プロジェクトは、ルーティングのために他のプロジェクトに接続できる意図に対してのみ利用できます。 

## <a name="how-do-i-use-entity-components"></a>エンティティ コンポーネントはどのように使いますか?

[エンティティ コンポーネント](./concepts/entity-components.md)に関する記事をご覧ください。

## <a name="which-languages-are-supported-in-this-feature"></a>この機能でサポートされている言語は何ですか?

[言語のサポート](./language-support.md)に関する記事をご覧ください。

## <a name="how-do-i-get-more-accurate-results-for-my-project"></a>プロジェクトで得られる結果をさらに正確にするにはどうすればよいですか?

精度の向上については、[推奨されるガイドライン](./how-to/build-schema.md#guidelines-and-recommendations)に関する記事をご覧ください。

## <a name="how-do-i-get-predictions-in-different-languages"></a>異なる言語で予測を取得するにはどうすればよいですか?

任意の言語で会話プロジェクトをトレーニングしてデプロイしたら、そのクエリを[複数の言語](./concepts/multiple-languages.md)ですぐに試すことができます。 言語によって結果が異なる場合があります。 任意の言語の精度を向上させるには、その言語でプロジェクトに発話を追加して、その言語のさらに多くの構文にトレーニング済みモデルを導入します。

## <a name="how-many-intents-entities-utterances-can-i-add-to-a-project"></a>プロジェクトに追加できる意図、エンティティ、発話の数はいくつですか?

[サービスの制限](./service-limits.md)に関する記事をご覧ください。 

## <a name="can-i-label-the-same-word-as-2-different-entities"></a>同じ単語に 2 つの異なるエンティティとしてラベルを付けることはできますか?

LUIS とは異なり、同じテキストに 2 つの異なるエンティティとしてラベルを付けることはできません。 異なるエンティティの学習済みコンポーネントは相互に排他的であり、1 つの文字セットに対しては 1 つの学習済みスパンだけが予測されます。

## <a name="can-i-import-a-luis-json-file-into-conversational-language-understanding"></a>会話言語理解に LUIS の JSON ファイルをインポートすることはできますか?

はい。サービスの最新バージョンから[任意の LUIS アプリケーション](./concepts/backwards-compatibility.md)の JSON ファイルをインポートできます。

## <a name="can-i-import-a-luis-lu-file-into-conversational-language-understanding"></a>会話言語理解に LUIS の `.LU` ファイルをインポートすることはできますか?

いいえ、サービスでサポートされているのは JSON 形式のみです。 LUIS に移動して、`.LU` ファイルをインポートし、JSON ファイルとしてエクスポートすることができます。 

## <a name="is-there-any-sdk-support"></a>SDK はサポートされていますか?

はい。予測についてだけであり、[サンプルを利用する](https://aka.ms/cluSampleCode)ことができます。 現在、SDK の作成はサポートされていません。

## <a name="are-there-apis-for-this-feature"></a>この機能のための API はありますか?

はい、すべての API を[利用することができます](https://aka.ms/clu-apis)。

## <a name="next-steps"></a>次のステップ

[会話言語理解の概要](overview.md)
