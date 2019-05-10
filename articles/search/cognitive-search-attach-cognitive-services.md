---
title: Cognitive Services リソースをスキルセットにアタッチする - Azure Search
description: Azure Search で Cognitive Services オールインワン サブスクリプションをコグニティブ エンリッチメント パイプラインにアタッチする手順。
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bad64f439d45581f8f4b55ea1ac849db1e27cb76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024581"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Azure Search で Cognitive Services リソースをスキルセットにアタッチする 

AI アルゴリズムでは、Azure Search での非構造化データの処理に使用される[コグニティブ インデックス パイプライン](cognitive-search-concept-intro.md)が制御されます。 これらのアルゴリズムは、画像分析および光学式文字認識 (OCR) 用の [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) やエンティティ認識、キー フレーズ抽出、その他のエンリッチメント用の [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) などの [Cognitive Services リソース](https://azure.microsoft.com/services/cognitive-services/)に基づきます。

限られた数のドキュメントであれば無料で強化できます。また、有料の Cognitive Services リソースをアタッチすれば、さらに大きくて複雑なワークロードに対応できます。 この記事では、Cognitive Services リソースをコグニティブ スキルセットと関連付けることによって、[Azure Search のインデックス作成](search-what-is-an-index.md)の間にデータを充実させる方法について説明します。

パイプラインが、Cognitive Services APIs と無関係なスキルで構成されている場合は、Cognitive Services リソースをアタッチする必要があります。 そうすることで、1 日あたりのエンリッチメントを少量に制限する **Free** リソースがオーバーライドされます。 Cognitive Services APIs にバインドされていないスキルの料金はかかりません。 これらのスキルには、[カスタム スキル](cognitive-search-create-custom-skill-example.md)、[テキスト マージャー](cognitive-search-skill-textmerger.md)、[テキスト スプリッター](cognitive-search-skill-textsplit.md)、[Shaper](cognitive-search-skill-shaper.md) などがあります。

> [!NOTE]
> 処理の頻度を増やしたり、ドキュメントを追加したり、AI アルゴリズムを追加したりすることによってスコープを拡大する場合は、請求対象の Cognitive Services リソースをアタッチする必要があります。 Cognitive Services の API を呼び出すとき、および Azure Search のドキュメントクラッキング段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Search の価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。

## <a name="use-free-resources"></a>無料リソースを使用する

コグニティブ検索のチュートリアルとクイック スタートの演習を完了するため、制限された無料の処理オプションを使用することができます。 

**無料 (制限付きのエンリッチメント)** は、サブスクリプションごとに、1 日あたり 20 ドキュメントに制限されます。

1. **データのインポート** ウィザードを開きます。

   ![データのインポート コマンド](media/search-get-started-portal/import-data-cmd2.png "データのインポート コマンド")

1. データ ソースを選択し、**[認知検索を追加します (省略可能)]** に進みます。 このウィザードの詳細な手順については、[ポータル ツールを使用したインポート、インデックス作成、クエリ](search-get-started-portal.md)に関する記事をご覧ください。

1. **[Cognitive Services をアタッチする]** を展開し、**[無料 (制限付きのエンリッチメント)]** を選択します。

   ![[Cognitive Services をアタッチする] セクションを展開](./media/cognitive-search-attach-cognitive-services/attach1.png "[Cognitive Services をアタッチする] を展開")

次の **[エンリッチメントの追加]** ステップに進みます。 ポータルで使用可能なスキルの説明については、「[手順 2:コグニティブ スキルを追加する](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)」(コグニティブ検索クイック スタート) をご覧ください。

## <a name="use-billable-resources"></a>課金対象のリソースを使用する

1 日のエンリッチメントが 20 を超えるワークロードでは、課金対象の Cognitive Services リソースをアタッチする必要があります。 

Cognitive Services APIs を呼び出すスキルに対してのみ課金されます。 [カスタム スキル](cognitive-search-create-custom-skill-example.md)、[テキスト マージャー](cognitive-search-skill-textmerger.md)、[テキスト スプリッター](cognitive-search-skill-textsplit.md)、[Shaper](cognitive-search-skill-shaper.md) スキルなどの API ベースでないスキルは課金されません。

1. **データのインポート** ウィザードを開き、データ ソースを選択し、**[認知検索を追加します (省略可能)]** に進みます。 

1. **[Cognitive Services をアタッチする]** を展開してから、**[新しい Cognitive Services リソースを作成します]** を選択します。 新しいタブが開き、リソースを作成できるようになります。 

   ![Cognitive Services リソースの作成](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services リソースの作成")

1. [場所] では、Azure Search と同じリージョンを選択し、リージョン間の送信帯域幅に課金されないようにします。

1. [価格レベル] では、**[S0]** を選択し、Cognitive Services の機能のオールインワン コレクション (Azure Search で使用される事前定義済みスキルをサポートする Vision および Language の機能を含む) を取得します。 

   S0 レベルの場合、特定のワークロードの料金は、[Cognitive Services の価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/)で確認できます。
  
   + **[プランの選択]** で *[Cognitive Services]* が選択されていることを確認します。
   + [Language features]\(Language の機能\) の下で、*[Text Analytics Standard]\(Text Analytics Standard\)* の料金が AI インデックスに適用されます。 
   + [Vision features]\(Vision の機能\) の下で、*[Computer Vision S1]\(Computer Vision S1\)* の料金が適用されます。

1. **[作成]** をクリックして、新しい Cognitive Services リソースをプロビジョニングします。 

1. **データのインポート** ウィザードが含まれる前のタブに戻ります。 **[更新]** をクリックして Cognitive Services リソースを表示し、リソースを選択します。

   ![Cognitive Services リソースを選択](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services リソースを選択")

1. **[エンリッチメントの追加]** セクションを展開して、データに対して実行する特定のコグニティブ スキルを選択し、フローの残りの部分に進みます。 ポータルで使用可能なスキルの説明については、「[手順 2:コグニティブ スキルを追加する](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)」(コグニティブ検索クイック スタート) をご覧ください。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>既存のスキルセットを Cognitive Services リソースにアタッチする

既存のスキルがある場合は、新規または別の Cognitive Services リソースにアタッチできます。

1. **[サービスの概要]** ページで、**[スキルセット]** をクリックします。

   ![スキルセット タブ](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "スキルセット タブ")

1. スキルセットの名前をクリックし、既存のリソースを選択するか、新しいリソースを作成します。 **[OK]** をクリックして変更を確認します。 

   ![スキルセット リソースの一覧](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "スキルセット リソースの一覧")

**[無料 (制限付きのエンリッチメント)]** は 1 日あたり 20 ドキュメントに制限されていること、および **[新しい Cognitive Services リソースを作成します]** は新しい課金対象のリソースをプロビジョニングするために使用することを思い出してください。 新しいリソースを作成した場合は、**[更新]** を選択して Cognitive Services リソースのリストを更新し、リソースを選択します。

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services をプログラムでアタッチする

プログラムでスキルセットを定義するときは、`cognitiveServices` セクションをスキルセットに追加します。 このセクションで、スキルセットを関連付ける Cognitive Services リソースのキーを追加します。 リソースは Azure Search と同じリージョンに存在する必要があることを思い出してください。 `@odata.type` も追加し、それを `#Microsoft.Azure.Search.CognitiveServicesByKey` に設定します。 

次の例は、このパターンを示しています。 定義の下部にある cognitiveServices セクションに注目してください

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>例:コストの見積もり

コグニティブ検索のインデックス作成に関連するコストを見積もるには、最初に、いくつかの値を得られるように平均的なドキュメントがどのようなものかを想定します。 たとえば、推定のため、次のような近似値を使用します。

+ 1000 PDF
+ 各 6 ページ
+ 1 ページに 1 画像 (6000 画像)
+ 1 ページあたり 3000 文字

画像とテキストの抽出、画像の光学式文字認識 (OCR)、組織のエンティティ認識を含む各 PDF のドキュメント解析で構成されるパイプラインを想定します。 

この演習では、トランザクションあたり最も高い価格を使用します。 段階的価格設定のため、実際のコストはもっと安い可能性があります。 「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services)」を参照してください。

1. テキストと画像のコンテンツを含むドキュメント解読の場合、現在、テキスト抽出は無料です。 6,000 個の画像については、抽出される 1,000 画像ごとに 1 ドルと想定して、このステップのコストは 6.00 ドルになります。

2. 6,000 画像の英語での OCR については、OCR コグニティブ スキルでは最適なアルゴリズム (DescribeText) が使用されます。 1,000 個のイメージを分析するたびに $2.50 かかるとすると、この手順に対して $15.00 が課金されることになります。

3. エンティティの抽出では、ページごとに合計 3 つのテキスト レコードが含まれます。 各レコードは 1,000 文字です。 ページあたり 3 つのテキスト レコード * 6,000 ページ = 18,000 個のテキスト レコードとなります。 1,000 個のテキスト レコードごとに $2.00 かかるとすると、この手順のコストは $36.00 になります。

すべてを考え合わせると、示されたスキルセットを含むこの種の PDF ドキュメントを 1,000 個取り込むために約 57.00 ドルが課金されることになります。 

## <a name="next-steps"></a>次の手順
+ [「Azure Search の価格」ページ](https://azure.microsoft.com/pricing/details/search/)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
