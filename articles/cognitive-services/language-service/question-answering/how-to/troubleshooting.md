---
title: トラブルシューティング - 質問応答
description: 質問応答についてきわめて多く寄せられる質問を精選した一覧です。機能の採用にかかる期間を短縮し、成果を上げるためにお役立てください。
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: dfafd5185c62d3011dfef66eb9d244ebe8a9b549
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477837"
---
# <a name="troubleshooting-for-question-answering"></a>質問応答のトラブルシューティング

質問応答についてきわめて多く寄せられる質問を精選した一覧です。機能の採用にかかる期間を短縮し、成果を上げるためにお役立てください。

## <a name="manage-predictions"></a>予測を管理する

<details>
<summary><b>クエリ予測のスループットのパフォーマンスを向上させるにはどうすればよいですか。</b></summary>

**回答**: スループットのパフォーマンスの問題は、Cognitive Search をスケールアップする必要があることを示しています。 パフォーマンスを向上させるには、Cognitive Search にレプリカを追加することを検討してください。

価格レベルの詳細については[こちら](../Concepts/azure-resources.md)をご覧ください。
</details>

## <a name="manage-your-project"></a>プロジェクトの管理

<details>
<summary><b>指定した URL/ファイルから質問とその回答のペアが抽出されません。なぜでしょうか。</b></summary>

**回答**: 質問応答は、有効な FAQ URL であっても、一部の Q&A (QnA) コンテンツを自動抽出できない場合があります。 そのような場合は、QnA コンテンツを .txt ファイルに貼り付けて、ツールによる取り込みが可能かどうかを確認できます。 または、[Language Studio ポータル](https://language.azure.com)経由でプロジェクトまたはナレッジ ベースに編集という形でコンテンツを追加することもできます。

</details>

<details>
<summary><b>作成できるナレッジ ベースの最大サイズはどれくらいですか。</b></summary>

**回答**: ナレッジ ベースのサイズは、QnA Maker サービスの作成時に選択した Azure Search の SKU により異なります。 詳細については、[こちら](../concepts/azure-resources.md)を参照してください。

</details>

<details>
<summary><b>複数人でナレッジ ベースを共有するにはどうすればよいですか?</b></summary>

**Answer**: 共有は、言語リソースのレベルで動作します。つまり、言語リソースと関連付けられているすべてのナレッジ ベースを共有できます。
</details>

<details>
<summary><b>ナレッジ ベースを変更するために、同じ Azure Active Directory テナントに含まれない共同作成者とナレッジ ベースを共有できますか。</b></summary>

**回答**: 共有は、Azure ロールベースのアクセス制御 (Azure RBAC) に基づきます。 Azure で他のユーザーと "_任意の_" リソースを共有できる場合、質問応答を共有することもできます。

</details>

<details>
<summary><b>5 人のユーザーに、それぞれが 1 つだけの質問応答ナレッジ ベースにアクセスできるように、読み取りと書き込みの権限を割り当てることができますか。</b></summary>

**回答**: 個々のナレッジ ベースではなく、言語リソース全体を共有できます。

</details>

<details>
<summary><b>ナレッジ ベースに対して行った更新が運用環境に反映されません。なぜですか。</b></summary>

**回答**: 編集内容をデプロイするには、テーブルの更新、テスト、設定のいずれであってもすべて保存する必要があります。 変更後は必ず **[保存]** を選択し、それらの変更が運用環境に反映されるようにプロジェクトを再デプロイしてください。

</details>

<details>
<summary><b>ナレッジ ベースは、リッチ データやマルチメディアに対応していますか?</b></summary>

**回答**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>ファイルと URL のマルチメディア自動抽出

* URL - HTML から Markdown への変換機能は制限されています。
* ファイル - サポートされていません

#### <a name="answer-text-in-markdown"></a>マークダウンの回答テキスト

QnA ペアをナレッジ ベースに追加すると、回答のマークダウン テキストを編集して、パブリック URL から使用できるメディアへのリンクを含めることができます。

</details>

<details>
<summary><b>質問応答では、英語以外の言語がサポートされていますか。</b></summary>

**回答**: 詳細については、[サポートされている言語](../language-support.md)に関するページを参照してください。

多言語コンテンツが含まれる場合は、言語ごとにプロジェクトを作成してください。

</details>

## <a name="manage-service"></a>サービスの管理

<details>
<summary><b>既存の Search Service を削除してしまいました。どうしたらいいですか。</b></summary>

**回答**: Azure Cognitive Search インデックスを削除した場合、この操作は最終的なものであり、インデックスを復旧することはできません。

</details>

<details>
<summary><b>Search Service の `testkbv2` インデックスを削除してしまいました。どうしたらいいですか。</b></summary>

**回答**: Search Service で `testkbv2` インデックスを削除した場合は、最後に発行されたナレッジ ベースのデータを復元できます。 GitHub で提供されている回復ツールの [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd) を使用してください。

</details>

<details>
<summary><b>複数の言語を使用したナレッジ ベースに同じ Azure Cognitive Search リソースを使用できますか?</b></summary>

**回答**: 複数の言語と複数のナレッジ ベースを使用するには、ユーザーは言語ごとにプロジェクトを作成する必要があります。また、言語リソース用に作成する最初のプロジェクトで、 **[I want to select the language when I create a project in this resource]\(このリソースでプロジェクトを作成するときに言語を選択します\)** オプションを選択する必要があります。 これにより、言語ごとに個別の Azure Search サービスが作成されます。

</details>

## <a name="integrate-with-other-services-including-bots"></a>ボットなどの他のサービスとの統合

<details>
<summary><b>質問応答を使用するために Bot Framework を使用する必要がありますか。</b></summary>

**回答**: いいえ。質問応答で [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) を使用する必要はありません。 ただし、質問応答は [Azure Bot Service](/azure/bot-service/) のいくつかのテンプレートの 1 つとして提供されています。 Bot Service では、Microsoft Bot Framework を使用してインテリジェントなボットを迅速に開発し、サーバーレス環境で実行することができます。

</details>

<details>
<summary><b>質問応答で新しいボットを作成するにはどうすればよいですか。</b></summary>

**回答**: Azure Bot Service でボットを作成するには、[こちら](../tutorials/bot-service.md)のドキュメントの手順に従ってください。

</details>

<details>
<summary><b>既存の Azure Bot Service で別のナレッジ ベースを使用するにはどうすればよいですか?</b></summary>

**回答**: 次のナレッジ ベースに関する情報が必要です。

* ナレッジ ベース ID。
* ナレッジ ベースの公開されたエンドポイント カスタム サブドメイン名 (`host` と呼ばれます)。公開後に **[設定]** ページで見つかります。
* ナレッジ ベースの公開されたエンドポイント キー - 公開後に **[設定]** ページで見つかります。

この情報を使用して、Azure portal でご利用のボットのアプリ サービスにアクセスします。 **[設定] -> [構成] -> [アプリケーション設定]** で、これらの値を変更します。

ナレッジ ベースのエンドポイント キーは、ABS サービスで `QnAAuthkey` としてラベルが付けられます。

</details>

<details>
<summary><b>2 つ以上のクライアント アプリケーションでナレッジ ベースを共有できますか?</b></summary>

**回答**: はい。ナレッジ ベースには、任意の数のクライアントからクエリを実行できます。

</details>

<details>
<summary><b>Web サイトに質問応答を埋め込むにはどうすればよいですか。</b></summary>

**回答**: Web サイトに質問応答サービスを Web チャット コントロールとして埋め込むには、次の手順に従ってください。

1. [こちら](../tutorials/bot-service.md)の手順に従って、FAQ ボットを作成します。
2. [こちら](../tutorials/bot-service.md#integrate-the-bot-with-channels)の手順に従って、Web チャットを有効にします。

## <a name="data-storage"></a>データ ストレージ

<details>
<summary><b>どのようなデータが、どこに保存されますか。</b></summary>

**回答**:

質問応答の言語リソースを作成するときに、Azure リージョンを選択しました。 ナレッジ ベースとログ ファイルが、このリージョンに格納されます。

</details>
