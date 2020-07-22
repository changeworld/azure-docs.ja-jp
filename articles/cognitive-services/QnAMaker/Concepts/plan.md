---
title: アプリの計画 - QnA Maker
description: QnA Maker アプリを計画するには、QnA Maker の動作と他の Azure サービスとのやりとりの方法、およびいくつかのサポート技術情報の概念を理解する必要があります。
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875385"
---
# <a name="plan-your-qna-maker-app"></a>QnA Maker アプリの計画

QnA Maker アプリを計画するには、QnA Maker の動作と他の Azure サービスとのやりとりの方法、およびいくつかのサポート技術情報の概念を理解する必要があります。

## <a name="azure-resources"></a>Azure リソース

QnA Maker で作成される各 [Azure リソース](azure-resources.md#resource-purposes)には特定の目的があります。 各リソースには独自の目的、制限、および[価格レベル](azure-resources.md#pricing-tier-considerations)があるため、これらのリソースが計画プロセスの一部としてどのように実行されるかを理解することが重要です。

|リソース|目的|
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) リソース|作成とクエリ予測|
| [Cognitive Search](azure-resources.md#cognitive-search-resource) リソース|データ ストレージと検索|
| [App Service のリソースと App Plan サービス](azure-resources.md#app-service-and-app-service-plan)のリソース|予測エンドポイントに対するクエリの実行|
| [Application Insights](azure-resources.md#application-insights) リソース|クエリ予測テレメトリ|

### <a name="resource-planning"></a>リソースの計画

作成とクエリ予測を学習している場合は、各リソースの Free レベル `F0` を使用すると適切に機能し、作成とクエリ予測の両方のエクスペリエンスが提供されます。 実稼働、ライブ、シナリオに移行するときは、リソースの選択を再評価してください。

#### <a name="qna-maker-resource"></a>QnA Maker リソース

1 つの QnA Maker リソースで、複数のナレッジ ベースをホストできます。 ナレッジ ベースの数は、Cognitive Search 価格レベルのサポートされているインデックスの数量によって決まります。 詳細については、[ナレッジ ベースとのインデックスの関係](azure-resources.md#index-usage)に関するページを参照してください。

#### <a name="knowledge-base-size-and-throughput"></a>ナレッジ ベースのサイズとスループット

実際のアプリを構築する予定の場合は、ナレッジ ベースのサイズと予想されるクエリ予測要求に合わせてリソースを計画します。

ナレッジ ベースのサイズは、次によって制御されます。
* [Cognitive Search リソース](../../../search/search-limits-quotas-capacity.md)の価格レベルの制限
* [QnA Maker の制限](../limits.md)

ナレッジ ベースのクエリ予測要求は、Web アプリ プランと Web アプリによって制御されます。 価格レベルを計画する場合は、「[推奨設定](azure-resources.md#recommended-settings)」を参照してください。

### <a name="resource-sharing"></a>リソースの共有

これらのリソースの一部を既に使用している場合は、リソースの共有を検討してください。 一部のリソースは[共有できます](azure-resources.md#share-services-with-qna-maker)が、これは高度なシナリオです。

同じ QnA Maker リソースで作成されたすべてのナレッジ ベースは、同じ**テスト** クエリ予測エンドポイントを共有します。

### <a name="understanding-impact-of-resource-selection"></a>リソース選択の影響について

適切なリソースの選択とは、ナレッジ ベースがクエリ予測に正常に回答することを意味します。

ナレッジ ベースが正常に機能していない場合、問題は、通常は不適切なリソース管理です。

リソースの選択が不適切な場合は、[変更する必要があるリソース](azure-resources.md#when-to-change-a-pricing-tier)を特定するために調査が必要です。

## <a name="knowledge-bases"></a>ナレッジ ベース

ナレッジ ベースは、その QnA Maker リソースに直接関連付けられ、クエリ予測要求に回答するために使用される質問と回答 (QnA) のペアを保持します。

### <a name="language-considerations"></a>言語に関する注意点

QnA Maker リソースに作成された 1 つ目のナレッジ ベースによって、リソースの言語が設定されます。 1 つの QnA Maker リソースには 1 つの言語のみを使用できます。

クエリをクエリ予測エンドポイントに送信する前に、QnA Maker リソースを言語ごとに構築するか、[Translator](../../translator/translator-info-overview.md) を使用してクエリを別の言語からナレッジ ベースの言語に変更します。

### <a name="ingesting-data-sources"></a>データ ソースの取り込み

ナレッジ ベースの作成に使用される取り込まれた[データ ソース](knowledge-base.md)は、次のいずれかにすることができます。

* パブリック URL
* SharePoint のプライベート URL
* ファイル

インジェスト プロセスでは、[サポートされているコンテンツの種類](content-types.md)がマークダウンに変換されます。 *回答*のそれ以上の編集はすべて、マークダウンを使用して行われます。 ナレッジ ベースを作成した後は、[リッチ テキスト作成](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)を使用して、QnA Maker ポータルで [QnA ペア](question-answer-set.md)を編集できます。

### <a name="data-format-considerations"></a>データ形式に関する考慮事項

QnA ペアの最終的な形式はマークダウンであるため、[マークダウンのサポート](../reference-markdown-format.md)について理解することが重要です。

QnA Maker は画像を含むコンテンツの認証を提供しないため、リンクされたイメージを QnA Maker ポータルのテスト ウィンドウと任意のクライアント アプリケーションで表示するためには、パブリック URL から使用できる必要があります。

### <a name="bot-personality"></a>ボットのパーソナリティ

[おしゃべり](../how-to/chit-chat-knowledge-base.md)を使用して、ナレッジ ベースにボットのパーソナリティを追加します。 このパーソナリティは、*Professional* や *Friendly* などの特定の口調で提供される回答を返します。 このおしゃべりは会話のセットとして提供され、追加、編集、および削除を完全に制御できます。

ボットがナレッジ ベースに接続している場合は、ボットのパーソナリティが推奨されます。 複数のサービス (そのうちの 1 つがナレッジ ベース) に接続している場合は、ナレッジ ベースでおしゃべりを引き続き使用することを選択できますが、ボット サービスがどのように相互作用するかを確認して、それが使用に適したアーキテクチャ設計であるかどうかを把握しておく必要があります。

### <a name="conversation-flow-with-a-knowledge-base"></a>ナレッジ ベースとの会話フロー

会話フローは、通常は `Hi` や `Hello` などのユーザーからのあいさつで始まります。 ナレッジ ベースは、`Hi, how can I help you` などの一般的な回答で答えることができ、会話を続けるためのフォローアップ プロンプトを選択することもできます。

会話フローは、ユーザーがボットの使用方法を認識し、会話中にボットによって中断されないように、ループを念頭に置いて設計する必要があります。 [フォローアップ プロンプト](../how-to/multiturn-conversation.md)は、会話フローを可能にする QnA ペア間のリンクを提供します。

### <a name="authoring-with-collaborators"></a>コラボレーターとの作成

コラボレーターは、ナレッジ ベース アプリケーションの完全な開発スタックを共有する他の開発者であることも、ナレッジ ベースの作成のみに制限されていることもあります。

ナレッジ ベースの作成では、コラボレーターの機能の範囲を制限するために、Azure portal で適用するいくつかの[ロールベースのアクセス許可](../how-to/collaborate-knowledge-base.md)をサポートしています。

## <a name="integration-with-client-applications"></a>クライアント アプリケーションとの統合

[クライアント アプリケーション](integration-with-other-applications.md)との統合は、予測ランタイム エンドポイントにクエリを送信することを意味します。 クエリは、QnA Maker の Web アプリ エンドポイントへの SDK または REST ベースの要求と共に、特定のナレッジ ベースに送信されます。

クライアント要求を正しく認証するためには、クライアント アプリケーションが正しい資格情報とナレッジ ベース ID を送信する必要があります。 Azure Bot Service を使用する場合は、Azure portal でボット構成の一部として設定を構成します。

### <a name="conversation-flow-in-a-client-application"></a>クライアント アプリケーションでの会話フロー

Azure ボットなどの[クライアント アプリケーション](integration-with-other-applications.md)での会話フローでは、ナレッジ ベースとの対話の前後に機能が必要になる場合があります。

クライアント アプリケーションで会話フローがサポートされている場合は、フォローアップ プロンプトやおしゃべりを処理する別の方法を使用して、これらを早期に設計し、クライアント アプリケーションで使用しているクエリが、別のサービスによって、またはナレッジ ベースに送信されるように、正しく処理されることを確認します。

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>QnA Maker と Language Understanding (LUIS) 間のディスパッチ

クライアント アプリケーションは複数の機能を提供する場合があり、ナレッジ ベースによって回答されるのはそのうちの 1 つのみです。 その他の機能でも、会話テキストを理解し、意味を引き出す必要があります。

一般的なクライアント アプリケーションのアーキテクチャでは、QnA Maker と [Language Understanding (LUIS)](../../LUIS/what-is-luis.md) の両方を一緒に使用します。 LUIS は、他のサービスに対するものも含むすべてのクエリのテキスト分類と抽出を提供し、QnA Maker はナレッジ ベースからの回答を提供します。

[共有アーキテクチャ](../choose-natural-language-processing-service.md)では、2 つのサービス間のディスパッチは、Bot Framework の[ディスパッチ](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) ツールを使用して行われます。

### <a name="active-learning-from-a-client-application"></a>クライアント アプリケーションからのアクティブ ラーニング

QnA Maker では、_アクティブ ラーニング_を使用して、回答に関する代わりの質問を提案することで、ナレッジ ベースを向上させます。 この[アクティブ ラーニング](active-learning-suggestions.md)の一部は、クライアント アプリケーションで行う必要があります。 クライアント アプリケーションは、会話プロンプトを使用して、ナレッジ ベースから返された回答が、ユーザーが期待していた回答ではなかったと判断したり、より適切な回答を判断したりできます。 クライアント アプリケーションは、予測品質を向上させるために、[ナレッジ ベースにその情報を返信する](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api)必要があります。

### <a name="providing-a-default-answer"></a>既定の回答の提供

ナレッジ ベースは、回答が見つからない場合は_既定の回答_を返します。 この回答は、QnA Maker ポータルの **[設定]** ページ、または [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body) から構成できます。

この既定の回答は、Azure ボットの既定の回答とは異なります。 Azure ボットの既定の回答は、構成設定の一部として Azure portal でお使いのボット用に構成され、スコアのしきい値が満たされていない場合に返されます。

## <a name="prediction"></a>予測

予測は、ナレッジ ベースからの応答であり、回答だけでなく、さらに多くの情報が含まれています。 クエリ予測応答を取得するには、[GeneateAnswer API](query-knowledge-base.md) を使用します。

### <a name="prediction-score-fluctuations"></a>予測スコアの変動

スコアは、次のいくつかの要因に基づいて変化することがあります。

* `top` プロパティを含む [GenerateAnswer](query-knowledge-base.md) への応答として要求された回答の数
* 利用できるさまざまな代わりの質問
* メタデータのフィルター処理
* `test` または `production` のナレッジ ベースに送信されるクエリ

[2 フェーズの回答の順位付け](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)があります。
* Cognitive Search - 最初の順位 - 回答が Cognitive Search から返されるようにするには、Cognitive Search から最適な回答が返されて、QnA Maker ランカーに渡すことができるように、_許可されている回答_の数が十分である必要があります
* QnA Maker - 2 番目の順位 - 特性付けと機械学習を適用して、最適な回答を判断します。

### <a name="service-updates"></a>サービスの更新情報

サービスの更新情報は、[最新のランタイム更新プログラム](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)を適用することによって自動的に管理されます。

### <a name="scaling-throughput-and-resiliency"></a>スケーリング、スループット、および回復性

スケーリング、スループット、および回復性は、[Azure リソース](../how-to/set-up-qnamaker-service-azure.md)、その価格レベル、および[トラフィック マネージャー](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)などの周囲のアーキテクチャによって決まります。

### <a name="analytics-with-application-insights"></a>Application Insights による分析

ナレッジ ベースに対するすべてのクエリは Application Insights に格納されます。 [上位のクエリ](../how-to/get-analytics-knowledge-base.md)を使用して、メトリックを理解します。

## <a name="development-lifecycle"></a>開発ライフサイクル

ナレッジ ベースの[開発ライフサイクル](development-lifecycle-knowledge-base.md)は、ナレッジ ベースの編集、テスト、および発行です。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker ペアのナレッジ ベースの開発

[QnA ペア](question-answer-set.md)は、クライアント アプリケーションの使用状況に基づいて設計および開発する必要があります。

各ペアには次のものを含めることができます。
* メタデータ - クエリ時の実行にフィルターを適用できます。 これにより、データのソース、コンテンツ、形式、および目的に関する追加情報を使用して、QnA ペアにタグを付けることができます。
* フォローアップ プロンプト - ユーザーが正しい回答を受け取るように、ナレッジ ベースからのパスを決定しします。
* 代わりの質問 - さまざまな形式の質問からの回答と検索を一致させるには、代わりの質問が重要です。 [アクティブ ラーニングの提案](active-learning-suggestions.md)は代わりの質問に変わります。

### <a name="devops-development"></a>DevOps 開発

DevOps パイプラインに挿入するナレッジ ベースを開発するには、[バッチ テスト](../quickstarts/batch-testing.md)中にナレッジ ベースを分離する必要があります。

ナレッジ ベースは、QnA Maker リソースの他のすべてのナレッジ ベースと Cognitive Search インデックスを共有します。 ナレッジ ベースはパーティションによって分離されますが、インデックスを共有すると、発行されたナレッジ ベースと比較したときにスコアの差が生じる可能性があります。

`test` と `production` のナレッジ ベースを_同じスコア_にするためには、QnA Maker リソースを 1 つのナレッジ ベースに分離します。 このアーキテクチャでは、リソースは分離されたバッチ テストの長さまで有効である必要があります。

## <a name="next-step"></a>次のステップ

* [Azure リソース](../how-to/set-up-qnamaker-service-azure.md)
* [質問と回答のペア](question-answer-set.md)