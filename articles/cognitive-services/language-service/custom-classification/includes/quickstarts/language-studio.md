---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 912aa02fadef0f950f5d89535bfe38133b61137b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092366"
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

[!INCLUDE [Language Studio project creation](../create-project.md)]

    
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
