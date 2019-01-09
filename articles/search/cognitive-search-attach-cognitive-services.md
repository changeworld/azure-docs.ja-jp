---
title: Cognitive Services をスキルセットをアタッチする - Azure Search
description: Cognitive Services オールインワン サブスクリプションをコグニティブ スキルセットにアタッチする手順
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317238"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Cognitive Services リソースをスキルセットと関連付ける 

> [!NOTE]
> 2018 年 12 月 21 日から、Azure Search のスキルセットに Cognitive Services リソースを関連付けることができるようになります。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従来課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格はプレビュー価格で課金され、[Azure Search 価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。


コグニティブ検索では、Azure Search で検索できるように、データを抽出および拡充します。 抽出とエンリッチメントの手順を*コグニティブ スキル*と呼びます。 コンテンツのインデックス作成中に呼び出されるスキルのセットは、*スキルセット*で定義されています。 スキルセットでは、[定義済みのスキル](cognitive-search-predefined-skills.md)またはカスタム スキルを使用できます (詳細については、[カスタム スキルの作成例](cognitive-search-create-custom-skill-example.md)に関するページを参照してください)。

この記事では、[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) リソースをコグニティブ スキルセットに関連付ける方法について説明します。

選択した Cognitive Services リソースにより、組み込みのコグニティブ スキルが強化されます。 このリソースは、課金目的でも使用されます。 組み込みのコグニティブ スキルを使用して実行したすべてのエンリッチメントは、選択した Cognitive Services リソースに対して課金されます。 これらの操作は、Cognitive Services リソースを使用して同じタスクを実行した場合と同じレートで課金されます。 「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Cognitive Services リソースが選択されていない場合の制限
2019 年 2 月 1 日より、Cognitive Services サブスクリプションをスキルセットと関連付けない場合、無料でエンリッチできるドキュメントは少数 (1 日に 20 個のドキュメント) に限られます。 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Cognitive Services リソースを新しいスキルセットと関連付ける

1. *[データのインポート]* エクスペリエンスの一環として、データ ソースに接続した後、*[認知検索を追加します]* (省略可能な手順) に移動します。 

1. *[Cognitive Services をアタッチする]* セクションを展開します。 これにより、Search Service と同じリージョンにあるすべての Cognitive Services リソースが表示されます。 
![[Cognitive Services をアタッチする] を展開](./media/cognitive-search-attach-cognitive-services/attach1.png "[Cognitive Services をアタッチする] を展開")

1. 既存の Cognitive Services リソースを選択するか、または *[Create a new Cognitive Services resource]\(新しい Cognitive Services リソースの作成\)* を選択します。 *[Free (Limited Enrichments)]/(無料 (制限付きエンリッチメント)/)* リソースを選択した場合は、少数のドキュメント (1 日に 20 個のドキュメント) のみを無料でエンリッチできます。 *[Create a new Cognitive Services resource]\(新しい Cognitive Services リソースの作成\)* をクリックした場合は、Cognitive Services リソースを作成するための新しいタブが開きます。 

1. 新しいリソースを作成した場合は、*[更新]* をクリックして Cognitive Services リソースのリストを更新し、リソースを選択します。 
![Cognitive Services リソースを選択](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services リソースを選択")

1. この操作を完了したら、*[エンリッチメントの追加]* セクションを展開して、データに対して実行する特定のコグニティブ スキルを選択し、フローの残りの部分に進みます。

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Cognitive Services リソースを既存のスキルセットと関連付ける

1. [サービスの概要] ページで、選 *[スキルセット]* タブを選択します。![[スキルセット] タブ](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "[スキルセット] タブ")

1. 変更するスキルセットを*クリックします*。 すると、スキルセットを編集するためのブレードが開きます。

1. 既存の Cognitive Services リソースを選択するか、または *[Create a new Cognitive Services resource]\(新しい Cognitive Services リソースの作成\)* を選択します。 *[Free (Limited Enrichments)]/(無料 (制限付きエンリッチメント)/)* リソースを選択した場合は、少数のドキュメント (1 日に 20 個のドキュメント) のみを無料でエンリッチできます。 *[Create a new Cognitive Services resource]\(新しい Cognitive Services リソースの作成\)* をクリックした場合は、Cognitive Services リソースを作成するための新しいタブが開きます。 <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. 新しいリソースを作成した場合は、*[更新]* をクリックして Cognitive Services リソースのリストを更新し、リソースを選択します。
1. *[OK]* をクリックして変更を確認します。

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Cognitive Services リソースをプログラムで関連付ける

スキルセットをプログラムで定義する際には、`cognitiveServices` セクションを追加します。 このセクションには、スキルセットと関連付ける Cognitive Services リソースのキーのほか、@odata.type を含める必要があります。これは、"#Microsoft.Azure.Search.CognitiveServicesByKey" に設定する必要があります。 次の例に、このパターンを示します。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>例:ドキュメント クラッキングおよびエンリッチメントのコストを見積もる
特定の種類のドキュメントをエンリッチするのにかかるコストを見積もりたい場合があります。 次の演習は参考用ですが、役に立つ場合があります。

1000 個の PDF があり、見積もり上、各ドキュメントが平均 6 ページで構成されているとします。 この演習では、ページごとに 1 つのイメージが含まれるとします。 また、平均で 1 ページあたり約 3,000 文字が含まれるとします。 

ここで、エンリッチメント パイプラインの一環として、次の手順を実行すると仮定します。
1. ドキュメント クラッキングの一環として、ドキュメントからコンテンツとイメージを抽出します。
1. エンリッチメントの一環として、抽出した各ページに対して OCR を実行し、すべてのページのテキストを結合して、すべてのイメージの結合済みのテキスト内の各組織を抽出します。

ここで、これらのドキュメントのインジェストにかかるコストを見積もってみましょう。

1000 個のドキュメントの場合: 

1. ドキュメント クラッキング。合計数 6,000 個のイメージが抽出されます。 1000 個のイメージを抽出するたびに $1 かかるとすると、コストは $6.00 になります。

2. これらの 6,000 個の各イメージからテキストを抽出します。 英語では、OCR コグニティブ スキルでは最適なアルゴリズム (DescribeText) が使用されます。 1,000 個のイメージを分析するたびに $2.50 かかるとすると、この手順に対して 15.00 ドルが課金されることになります。

3. エンティティの抽出では、ページごとに合計 3 つのテキスト レコード (各レコードは 1,000 文字) が含まれます。 3 つのテキスト レコード/ページ * 6,000 ページ = 18,000 個のテキスト レコードとなります。 1000 個のテキスト レコードごとに $2.00 かかるとすると、この手順のコストは $36.00 になります。

すべてを考え合わせると、示されたスキルセットを含むこの種の PDF ドキュメントを 1000 個インジェストするのに、$57.00 が課金されることになります。  この演習では、トランザクションあたりの最も高い価格を想定しているので、価格の変動によって価格が下がる可能性があります。 「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services)」を参照してください。



## <a name="next-steps"></a>次の手順
+ [「Azure Search の価格」ページ](https://azure.microsoft.com/pricing/details/search/)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)
