---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: c7018ad879e5877c131af041685722d30b2906b2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131519973"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)。

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>新しい Azure リソースと Azure Blob Storage アカウントを作成する

カスタム テキスト分類を使用する前に、Azure 言語リソースを作成する必要があります。このリソースにより、プロジェクトを作ってモデルのトレーニングを始めるために必要な資格情報が提供されます。 また、モデルの構築に使用するデータセットをアップロードできる Azure ストレージ アカウントも必要です。

> [!IMPORTANT]
> すぐに始めるには、以下で示す手順を使用して新しい Azure 言語リソースを作成することをお勧めします。これにより、リソースの作成とストレージ アカウントの構成を同時に行うことができ、後で行うより簡単です。
>
> 既存のリソースを使いたい場合は、それとストレージ アカウントの構成を個別に行う必要があります。 詳細については、[**プロジェクトの要件**](../../how-to/create-project.md#using-a-pre-existing-azure-resource)に関する記事を参照してください。

1. [Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) に移動し、新しい Azure 言語リソースを作成します。 追加機能の選択を求めるメッセージが表示される場合は、 **[Custom text classification & custom NER]\(カスタム テキスト分類とカスタム NER\)** を選びます。 リソースを作るときは、次のパラメーターを指定します。

    |Azure リソースの要件  |必須の値  |
    |---------|---------|
    |Location | "米国西部 2" または "西ヨーロッパ"         |
    |Pricing tier     | Standard (**S**) 価格レベル        |

2. **[Custom Named Entity Recognition (NER) & Custom Classification (Preview)]\(カスタム固有表現認識 (NER) とカスタム分類 (プレビュー)\)** セクションで、既存のストレージ アカウントを選ぶか、 **[新しいストレージ アカウントの作成]** を選びます。 これらの値はこのクイックスタート用であり、必ずしもご自分の運用環境で使用する[ストレージ アカウントの値](/azure/storage/common/storage-account-overview)ではないことに注意してください。

    |ストレージ アカウントの値  |推奨値  |
    |---------|---------|
    | 名前 | 任意の名前 |
    | パフォーマンス | Standard |
    | アカウントの種類| ストレージ (汎用 v1) |
    | レプリケーション | ローカル冗長ストレージ (LRS)
    |Location | 最適な待機時間にするため、最も近い任意の場所。        |


## <a name="upload-sample-data-to-blob-container"></a>サンプル データを BLOB コンテナーにアップロードする

Azure ストレージ アカウントを作成し、それを言語リソースにリンクしたら、このクイックスタート用のサンプル ファイルをアップロードする必要があります。 後でこれらのファイルを使用してモデルをトレーニングします。

1. このクイックスタートのサンプル データを [GitHub](https://go.microsoft.com/fwlink/?linkid=2175083) からダウンロードします。

2. [Azure portal](https://ms.portal.azure.com) で自分の Azure ストレージ アカウントに移動します。 自分のアカウントに移動し、サンプル データをそこにアップロードします。

用意されているサンプル データセットには、"ミステリー"、"ドラマー"、"スリラー"、"コメディ"、"アクション" というクラスの 1 つ以上に属する約 200 の映画の概要が含まれています。

## <a name="create-a-custom-classification-project"></a>カスタム分類プロジェクトを作成する

1. [Language Studio](https://aka.ms/languageStudio) にログインします。 サブスクリプションと言語リソースを選ぶためのウィンドウが表示されます。 上の手順で作成したリソースを選びます。

2. Language Studio の **[Classify text]\(テキストの分類\)** セクションで、利用可能なサービスから **[カスタム テキスト分類]** を選択します。

3. プロジェクト ページの上部メニューから、 **[Create new project]\(新しいプロジェクトの作成\)** を選択します。 プロジェクトを作成すると、データのタグ付け、モデルのトレーニング、評価、改善、デプロイを実行できます。 

    :::image type="content" source="../../media/create-project.png" alt-text="プロジェクト作成ページのスクリーンショット。" lightbox="../../media/create-project.png":::
<!--
4. If you have created your resource using the steps above, the **Connect storage** step will be completed already. You only need to do this step once for each resource you use and it is irreversible. If you connect a storage account to your resource, you cannot disconnect it later.

    :::image type="content" source="../../../custom-named-entity-recognition/media/connect-storage.png" alt-text="A screenshot showing the storage connection screen." lightbox="../../../custom-named-entity-recognition/media/connect-storage.png":::
-->
4. 上記の手順を使用してリソースを作成した場合、**ストレージの接続** 手順は既に完了しています。 そうでない場合は、リソースに接続する前に、[ストレージ アカウントのロール](../../how-to/create-project.md#roles-for-your-storage-account)を割り当てる必要があります

5. 名前、説明、プロジェクト内のファイルの言語など、プロジェクトの情報を入力します。 プロジェクトの名前は後で変更できません。
    >[!TIP]
    > データセットは、すべて同じ言語である必要はありません。 それぞれ異なるサポート言語の複数のファイルを含めることができます。 異なる言語のファイルがデータセットに含まれている場合、または実行時に異なる言語を想定している場合は、プロジェクトの基本情報を入力するときに **[enable multi-lingual dataset]\(多言語データセットを有効にする\)** を選択します。

6. プロジェクトの種類を選択します。 このクイック スタートでは、複数のクラスを同じファイルに割り当てることができる、複数ラベル分類プロジェクトを作成します。 続けて、 **[次へ]** をクリックします。 [プロジェクトの種類](../../glossary.md#project-types)の詳細

7. データをアップロードしたコンテナーを選びます。 このクイックスタートでは、コンテナー内にある既存のタグ ファイルを使用します。 続けて、 **[次へ]** をクリックします。

8. 入力したデータを確認し、 **[Create Project]\(プロジェクトの作成\)** を選びます。
    
## <a name="train-your-model"></a>モデルをトレーニングする

通常はプロジェクトを作った後、分類モデルをトレーニングするために、データをインポートし、その中の[エンティティのタグ付け](../../how-to/tag-data.md)を開始します。 このクイックスタートでは、以前ダウンロードして Azure ストレージ アカウントに格納したタグ付けされたデータ ファイルのサンプルを使用します。

モデルはテキストを分類するためにトレーニングされる機械学習オブジェクトです。 モデルではサンプル データから学習を行い、その後テクニカル サポート チケットを分類することができます。

モデルのトレーニングを開始するには:

1. 左側のメニューから **[トレーニング]** を選びます。

2. **[Train a new model]\(新しいモデルのトレーニング\)** を選び、下のテキスト ボックスにモデル名を入力します。

    :::image type="content" source="../../media/train-model.png" alt-text="トレーニングのモデルの選択ページを示すスクリーンショット" lightbox="../../media/train-model.png":::

3. ページの下部にある **[トレーニング]** ボタンをクリックします。

    > [!NOTE]
    > * トレーニング中、データは 2 つのセットに分割されます (トレーニング用に 80%、テスト用に 20%)。 データの分割の詳細については、[こちら](../../how-to/train-model.md#data-splits)を参照してください。
    > * トレーニングには最大で数時間かかる場合があります。

## <a name="deploy-your-model"></a>モデルをデプロイする


通常はモデルをトレーニングした後で、[評価の詳細](../../how-to/view-model-evaluation.md)を確認し、必要に応じて[改善を行います](../../how-to/improve-model.md)。 このクイックスタートでは、モデルを単にデプロイして試用できるようにします。

モデルのトレーニングが済んだら、モデルをデプロイできます。 モデルをデプロイすると、[分析 API](https://aka.ms/ct-runtime-swagger) を使用して、モデルをテキストの分類に使用できるようになります。

1. 左側のメニューから **[Deploy model]\(モデルのデプロイ\)** を選びます。

2. デプロイするモデルを選び、 **[Deploy model]\(モデルのデプロイ\)** を選びます。

## <a name="test-your-model"></a>モデルのテスト

モデルをデプロイすると、テキスト分類に使用できるようになります。 最初のテキスト分類要求を送信するには、次の手順を実行します。 

1. 左側のメニューから **[Test model]\(モデルのテスト\)** を選びます。

2. テストするモデルを選びます。

3. テキストをテキスト ボックスに追加します。`.txt` ファイルをアップロードすることもできます。 

4. **[Run the test]\(テストの実行\)** をクリックします。

5. **[Result]\(結果\)** タブで、テキストに対して予測されたクラスを確認できます。 **[JSON]** タブで JSON 応答を表示することもできます。 

    :::image type="content" source="../../media/test-model-results.png" alt-text="モデルのテスト結果を示すスクリーンショット。例は Microsoft によって &quot;CMU Movie Summary, CC BY-SA 3.0&quot; から変更されています" lightbox="../../media/test-model-results.png":::

## <a name="clean-up-projects"></a>プロジェクトをクリーンアップする

プロジェクトが不要になったら、[Language Studio のプロジェクト ページ](https://aka.ms/custom-classification
)から削除できます。 削除するプロジェクトを選び、 **[削除]** をクリックします。
