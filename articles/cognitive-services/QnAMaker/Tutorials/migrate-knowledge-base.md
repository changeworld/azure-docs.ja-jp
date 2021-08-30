---
title: ナレッジ ベースの移行 - QnA Maker
description: ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ea0a02366f2c2d2c3fd656d9a6dbce111d632422
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121211"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>エクスポート/インポートを使用したナレッジ ベースの移行

次のようなケースでは、ナレッジ ベースのコピーを作成するとよいかもしれません。

* QnA Maker GA からカスタム質問と回答にナレッジ ベースをコピーする 
* バックアップと復元のプロセスを導入する 
* CI/CD パイプラインと統合する 
* データを別のリージョンに移動する

## <a name="prerequisites"></a>前提条件

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

> * Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください。
> * Azure portal で作成された [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 リソースを作成するしたに選択した Azure Active Directory ID、サブスクリプション、QnA リソース名を覚えておいてください。
> * 新しい [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)をセットアップします

# <a name="custom-question-answering-preview-release"></a>[カスタム質問と回答 (プレビュー リリース)](#tab/v2)

> * Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください。
> * Azure portal でカスタム質問と回答機能が有効になっている [Text Analytics リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。 リソースを作成したときに選択した Azure Active Directory ID、サブスクリプション、Text Analytics リソース名を覚えておいてください。
> * [カスタム質問と回答](../How-To/set-up-qnamaker-service-azure.md)のセットアップ

---

## <a name="export-a-knowledge-base"></a>ナレッジ ベースをエクスポートする
1. [QnA Maker ポータル](https://qnamaker.ai)にサインインします。
1. 移行するナレッジ ベースを選択します。

1. **QnA**、**同意語**、**ナレッジ ベース レプリカ** をエクスポートするためのオプションが **[設定]** ページにあります。 データは .tsv または .xlsx でダウンロードするように選択できます。

   1. **[QnAs]\(QnA\)** : QnA をエクスポートすると、QnA ペア (質問、回答、メタデータ、フォローアップ プロンプト、データ ソース名を含む) がすべてダウンロードされます。 質問と回答と共にエクスポートされた QnA ID は、[update API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update) を使用して特定の QnA ペアを更新するために使用できます。 特定の QnA ペアの QnA ID は、複数のエクスポート操作で変更されることはありません。
   2. **[Synonyms]\(同意語\)** : ナレッジ ベースに追加された同意語をエクスポートできます。
   4. **[Knowledge Base Replica]\(ナレッジ ベース レプリカ\)** : 同意語やその他の設定を含むナレッジ ベース全体をダウンロードしたい場合は、このオプションを選択してください。

## <a name="import-a-knowledge-base"></a>ナレッジ ベースをインポートする
1. qnamaker.ai ポータルのトップ メニューから **[Create a knowledge base]\(ナレッジ ベースの作成\)** をクリックし、URL もファイルも追加せずに "_空の_" ナレッジ ベースを作成します。 新しいナレッジ ベースに任意の名前を設定し、 **[Create your KB]\(KB の作成\)** をクリックします。 

1. この新しいナレッジ ベースの **[Settings]\(設定\)** タブを開き、 _[Import knowledge base]\(ナレッジ ベースのインポート\)_ で、 **[QnAs]\(QnA\)** 、 **[Synonyms]\(同意語\)** 、 **[Knowledge Base Replica]\(ナレッジ ベース レプリカ\)** のいずれかのオプションを選択します。 

   1. **[QnAs]\(QnA\)** : QnA ペアがすべてインポートされます。 **新しいナレッジ ベースで作成された QnA ペアは、エクスポートされたファイルに存在するものと同じ QnA ID を持つ必要があります**。 QnA をインポートするには、[SampleQnAs.xlsx](https://aka.ms/qnamaker-sampleqnas)、[SampleQnAs.tsv](https://aka.ms/qnamaker-sampleqnastsv) を参照してください。
   2. **[Synonyms]\(同意語\)** : このオプションを使用して、ナレッジ ベースに同意語をインポートすることができます。 同意語をインポートするには、[SampleSynonyms.xlsx](https://aka.ms/qnamaker-samplesynonyms)、[SampleSynonyms.tsv](https://aka.ms/qnamaker-samplesynonymstsv) を参照してください。
   3. **[Knowledge Base Replica]\(ナレッジ ベース レプリカ\)** : このオプションを使用すると、QnA、同意語、設定を含む KB レプリカをインポートできます。 詳細については、[KBReplicaSampleExcel](https://aka.ms/qnamaker-samplereplica)、[KBReplicaSampleTSV](https://aka.ms/qnamaker-samplereplicatsv) を参照してください。 非構造化コンテンツもレプリカに追加したい場合は、[CustomQnAKBReplicaSample](https://aka.ms/qnamaker-samplev2replica) を参照してください。

      レプリカをインポートする際、QnA と非構造化コンテンツのどちらかは必須となります。 非構造化ドキュメントは、カスタム質問と回答でのみ有効です。
      レプリカをインポートするにあたり、同意語ファイルは必須ではありません。
      レプリカをインポートするにあたり、設定ファイルは必須です。

         |設定|QnA Maker KB へのインポート時に更新が許可されているか?|カスタム質問と回答 KB へのインポート時に更新が許可されているか?|
         |:--|--|--|
         |DefaultAnswerForKB|いいえ|はい|
         |EnableActiveLearning (True/False)|はい|いいえ|
         |EnableMultiTurnExtraction (True/False)|はい|はい|
         |DefaultAnswerforMultiturn|はい|はい|
         |言語|いいえ|いいえ|

1. テスト パネルを使用して、新しいナレッジ ベースを **テスト** します。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。

1. ナレッジ ベースを **公開** し、チャットボットを作成します。 [ナレッジ ベースの公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。

   > [!div class="mx-imgBorder"]
   > ![ナレッジ ベースの移行](../media/qnamaker-how-to-migrate-kb/import-export-kb.png)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>プログラムで QnA Maker からナレッジ ベースを移行する

次の REST API を使用して、プログラムで移行プロセスを利用できます。

**エクスポート**

* [ナレッジ ベースのダウンロード API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**[インポート]**

* [置換 API (同じナレッジ ベース ID での再読み込み)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [作成 API (新しいナレッジ ベース ID での読み込み)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs"></a>チャット ログ
チャットログを移行することはできません。新しいナレッジ ベースでは Application Insights を使ってチャットログが格納されているためです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-To/edit-knowledge-base.md)
