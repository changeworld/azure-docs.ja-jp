---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e8fa35bb99a11e51d634219e54296c5be831b3a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092286"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>新しい Azure リソースと Azure Blob Storage アカウントを作成する

カスタム NER を使う前に、Azure 言語リソースを作成する必要があります。このリソースにより、プロジェクトを作ってモデルのトレーニングを始めるために必要な資格情報が提供されます。 また、モデルの構築に使うデータセットをアップロードできる Azure ストレージ アカウントも必要です。

> [!IMPORTANT]
> すぐに始めるには、以下で示す手順を使用して新しい Azure 言語リソースを作成することをお勧めします。これにより、リソースの作成とストレージ アカウントの構成を同時に行うことができ、後で行うより簡単です。
>
> 既存のリソースを使いたい場合は、それとストレージ アカウントの構成を個別に行う必要があります。 詳細については、[プロジェクトの作成](../../how-to/create-project.md#using-a-pre-existing-azure-resource)に関する記事を参照してください。

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

Azure ストレージ アカウントを作成し、それを言語リソースにリンクしたら、このクイックスタート用のサンプル ファイルをアップロードする必要があります。 後でこれらのファイルを使ってモデルをトレーニングします。

1. GitHub からこのクイックスタート用の[サンプル データをダウンロード](https://go.microsoft.com/fwlink/?linkid=2175226)します。

2. [Azure portal](https://ms.portal.azure.com) でご自分の Azure ストレージ アカウントに移動します。 ご自分のアカウントに移動し、サンプル データをそこにアップロードします。

提供されているサンプル データセットには、20 件のローン契約が含まれ、各契約には貸し手と借り手の 2 つの当事者が含まれています。 提供されているサンプル ファイルを使って、両当事者の関連情報、契約日、ローン金額、利率を抽出できます。

## <a name="create-a-custom-named-entity-recognition-project"></a>カスタム固有表現認識プロジェクトを作成する

リソースとストレージ コンテナーを構成したら、新しい会話 NER プロジェクトを作成します。 プロジェクトは、データに基づいてカスタム AI モデルを構築するための作業領域です。 プロジェクトにアクセスできるのは、自分と、使用されている Azure リソースへの共同作成者アクセス権を持つユーザーのみです。

1. [Language Studio ポータル](https://aka.ms/languageStudio)にサインインします。 サブスクリプションと言語リソースを選ぶためのウィンドウが表示されます。 上の手順で作成したリソースを選びます。

2. **[エンティティの抽出]** セクションを見つけて、使用可能なサービスから **[Custom named entity recognition]\(カスタム固有表現認識\)** を選びます。

3. プロジェクト ページの上部のメニューで **[Create new project]\(新しいプロジェクトの作成\)** を選びます。 プロジェクトを作成すると、データのタグ付け、モデルのトレーニング、評価、改善、デプロイを実行できます。 

    
    :::image type="content" source="../../media/create-project.png" alt-text="プロジェクト作成ページのスクリーンショット。" lightbox="../../media/create-project.png":::


4.  **[Create new project]\(新しいプロジェクトの作成\)** をクリックすると、ストレージ アカウントを接続するための画面が表示されます。 ストレージ アカウントが見つからない場合は、前の手順を使ってリソースを作ったことを確認してください。 

    >[!NOTE]
    > * この手順は、使用する新しいリソースごとに 1 回だけ行う必要があります。 
    > * このプロセスは元に戻すことができません。ストレージ アカウントをリソースに接続すると、後で切断することはできません。
    > * リソースは 1 つのストレージ アカウントにのみ接続できます。
    > * ストレージ アカウントを既に接続している場合は、代わりに **[Enter basic information]\(基本情報の入力\)** 画面が表示されます。 次のステップを参照してください。
    
    :::image type="content" source="../../media/connect-storage.png" alt-text="ストレージ接続画面を示すスクリーンショット。" lightbox="../../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. 名前、説明、プロジェクト内のファイルの言語など、プロジェクト情報を入力します。 プロジェクトの名前を後で変更することはできません。 

6. データをアップロードしたコンテナーを選びます。 このクイックスタートでは、コンテナー内にある既存のタグ ファイルを使用します。 続けて、 **[次へ]** をクリックします。

7. 入力したデータを確認し、 **[Create Project]\(プロジェクトの作成\)** を選びます。

## <a name="train-your-model"></a>モデルをトレーニングする

通常はプロジェクトを作った後、分類モデルをトレーニングするために、データをインポートし、その中の[エンティティのタグ付け](../../how-to/tag-data.md)を開始します。 このクイックスタートでは、以前ダウンロードして Azure ストレージ アカウントに格納したタグ付けされたデータ ファイルのサンプルを使用します。

モデルはテキストを分類するためにトレーニングされる機械学習オブジェクトです。 モデルではサンプル データから学習を行い、その後テクニカル サポート チケットを分類することができます。

モデルのトレーニングを開始するには:

1. 左側のメニューから **[トレーニング]** を選びます。

2. **[Train a new model]\(新しいモデルのトレーニング\)** を選び、下のテキスト ボックスにモデル名を入力します。

    :::image type="content" source="../../media/train-model.png" alt-text="トレーニングのモデルの選択ページを示すスクリーンショット" lightbox="../../media/train-model.png":::

3. ページの下部にある **[トレーニング]** ボタンをクリックします。

    > [!NOTE]
    > * トレーニング中、データは 2 つのセットに分割されます (トレーニング用に 80%、テスト用に 20%)。 データの分割の詳細については、[こちら](../../how-to/train-model.md#data-split)を参照してください。
    > * トレーニングには最大で数時間かかる場合があります。

## <a name="deploy-your-model"></a>モデルをデプロイする

通常はモデルをトレーニングした後で、[評価の詳細](../../how-to/view-model-evaluation.md)を確認し、必要に応じて[改善を行います](../../how-to/improve-model.md)。 このクイックスタートでは、モデルを単にデプロイして試用できるようにします。

モデルのトレーニングが済んだら、モデルをデプロイできます。 モデルをデプロイしたら、使用を開始し、[Analyze API](https://aka.ms/ct-runtime-swagger) を使って名前付きエンティティを抽出することができます。

1. 左側のメニューから **[Deploy model]\(モデルのデプロイ\)** を選びます。

2. デプロイするモデルを選び、 **[Deploy model]\(モデルのデプロイ\)** を選びます。

## <a name="test-your-model"></a>モデルのテスト

モデルをデプロイした後、エンティティの抽出を行うために使用を開始することができます。 最初のエンティティ抽出要求を送信するには、次の手順を使用します。

1. 左側のメニューから **[Test model]\(モデルのテスト\)** を選びます。

2. テストするモデルを選びます。

3. テキストをテキスト ボックスに追加します。`.txt` ファイルをアップロードすることもできます。 

4. **[Run the test]\(テストの実行\)** をクリックします。

5. **[Result]\(結果\)** タブでは、テキストから抽出されたエンティティとその型を確認できます。 **[JSON]** タブで JSON 応答を表示することもできます。

    :::image type="content" source="../../media/test-model-results.png" alt-text="テスト結果を表示する" lightbox="../../media/test-model-results.png":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

プロジェクトが不要な場合は、[Language Studio](https://aka.ms/custom-extraction) を使ってプロジェクトを削除できます。 左側のナビゲーション メニューで **[Custom Named Entity Recognition (NER)]\(固有表現認識 (NER)\)** を選び、削除するプロジェクトを選び、 **[削除]** をクリックします。
