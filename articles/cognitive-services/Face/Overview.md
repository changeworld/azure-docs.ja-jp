---
title: Azure Face サービスとは
titleSuffix: Azure Cognitive Services
description: Azure Face サービスは、画像に含まれている人の顔を検出、認識、分析するために使用する AI アルゴリズムを提供します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 顔認識, 顔認識ソフトウェア, 顔分析, 顔照合, 顔認識アプリ, 画像による顔検索, 顔認識検索
ms.openlocfilehash: 26076289d8c6659abdd55fa805c27b13690feccd
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258743"
---
# <a name="what-is-the-azure-face-service"></a>Azure Face サービスとは

> [!WARNING]
> 2020 年 6 月 11 日に、Microsoft は、人権に基づく厳格な法令が制定されない限り、米国内の警察に顔認識テクノロジを販売しないことを発表しました。 このため、顧客は、米国内の警察である場合、または警察による顔認識機能および Azure サービスに含まれる機能 (Face や Video Indexer など) の使用を許可する場合、これらの機能を使用できません。

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Face サービスは、画像に含まれている人の顔の検出、認識、分析する AI アルゴリズムを提供します。 顔認識ソフトウェアは、セキュリティ、自然なユーザー インターフェイス、画像コンテンツの分析と管理、モバイル アプリ、ロボティクスなど、多種多様なシナリオで重要です。

Face サービスにはいくつかの異なる顔分析機能があり、そのそれぞれについて以降のセクションで説明します。

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./Quickstarts/client-libraries.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [概念の記事](./concepts/face-detection.md)では、サービスの機能と特長について詳しく説明します。
* [チュートリアル](./Tutorials/FaceAPIinCSharpTutorial.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

## <a name="face-detection"></a>顔検出

Detect API では、画像に含まれている人の顔を検出し、その位置の四角形の座標を返します。 顔検出では、オプションとして、頭部姿勢、性別、年齢、ひげ、眼鏡などの顔関連の属性を抽出できます。 これらの属性はおおよその予測であって、実際の分類ではありません。 

> [!NOTE]
> 顔検出機能は [Computer Vision サービス](../computer-vision/overview.md)でもご利用いただけます。 ただし、その他の Face 操作、たとえば識別、検証、類似検索、グループ化などを実行したい場合は、こちらの Face サービスを使用する必要があります。

![女性と男性の画像 (それぞれの顔の周囲に四角形が描かれ、年齢と性別が表示されている)](./Images/Face.detection.jpg)

顔検出の詳細については、[顔検出](concepts/face-detection.md)の概念に関するページを参照してください。 また、[Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) リファレンス ドキュメントも参照してください。

## <a name="face-verification"></a>顔検証

Verify API は、Detection をベースとしており、"その 2 つの画像は同一人物か?" という問いに答えるものです。 プローブ画像の比較対象となる登録済みテンプレートが 1 つだけであることから、検証は "一対一" のマッチングとも呼ばれます。 本人確認や出入管理のシナリオで、前もって撮影された画像 (政府発行の ID カードの写真など) と写真が一致していることを確認する目的に検証を使用できます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) リファレンス ドキュメントを参照してください。

## <a name="face-identification"></a>顔識別

Identify API も、Detection をベースとしており、"検出されたこの顔は、データベースに登録されているいずれかの顔と一致するか?" という問いに答えるものです。 顔認識検索に似ているため、"一対多" のマッチングとも呼ばれます。 検出された顔を含むプローブ テンプレートと登録済みの各テンプレートとの類似の程度に基づいて、一致候補が返されます。

次の画像は、`"myfriends"` という名前のデータベースの例を示しています。 各グループは、最大で 100 万個の異なる person オブジェクトを含むことができます。 各 person オブジェクトには最大で 248 個の顔を登録できます。

![それぞれ異なる人物に対応する 3 つの列とそれぞれ 3 行の顔の画像から成るグリッド](./Images/person.group.clare.jpg)

データベースを作成してトレーニングした後、新しく検出された顔を含むグループに対して識別を実行できます。 顔がグループ内の person として識別された場合、その person オブジェクトが返されます。

人物の特定の詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) リファレンス ドキュメントを参照してください。

## <a name="find-similar-faces"></a>似た顔の検索

Find Similar API では、ターゲットの顔と候補となる一連の顔との間で顔照合を行い、ターゲットの顔によく似ている一連の顔が検索されます。 これは、画像による顔検索を行う場合に便利です。 

**matchPerson** と **matchFace** の 2 つの動作モードがサポートされています。 **matchPerson** モードでは、[Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) を使用して同一人物についてフィルター処理が行われた後、似た顔が返されます。 **matchFace** モードでは、同一人物フィルターは無視されます。 同一人物のものであるかどうかに関係なく、似ている顔の候補のリストが返されます。

ターゲットの顔の例を次に示します。

![笑顔の女性](./Images/FaceFindSimilar.QueryFace.jpg)

そして候補となる顔の画像は次のとおりです。

![笑っている人々の 5 つの画像。 画像 a と画像 b は同一人物を示しています。](./Images/FaceFindSimilar.Candidates.jpg)

4 つの似た顔を検索する場合、**matchPerson** モードではターゲットの顔と同じ人を表す a と b が返されます。 **matchFace** モードでは、ターゲットと同一人物ではない、あるいは類似性が低くとも厳密に 4 つの候補が返されるので、a、b、c、d が返されます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) リファレンス ドキュメントを参照してください。

## <a name="face-grouping"></a>顔のグループ化

Group API では、未知の顔の集合が、類似性に基づいて複数のグループに分けられます。 それぞれのグループは、元の顔の集合から得られる、互いに素な真部分集合です。 グループ内のすべての顔は、同じ人物に属する可能性があります。 1 人の人物について、いくつかの異なるグループが存在する可能性があります。 グループは、たとえば表情などの別の要因によって区別されます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) リファレンス ドキュメントを参照してください。


## <a name="sample-apps"></a>サンプル アプリ

以下のサンプル アプリケーションは、Face サービスの使用方法をいくつか示しています。

- [Face API: Windows クライアント ライブラリとサンプル](https://github.com/Microsoft/Cognitive-Face-Windows) は、顔の検出、分析、識別についていくつかのシナリオを実証する WPF アプリです。
- [FamilyNotes UWP アプリ](https://github.com/Microsoft/Windows-appsample-familynotes)は、家族でノートを共有するシナリオにおいて、音声、Cortana、インク、カメラと共に顔の識別が使用されるユニバーサル Windows プラットフォーム (UWP) アプリです。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services リソース全般に言えることですが、Face サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、顔認識アプリの基本コンポーネントを任意の言語でコーディングします。

- [クライアント ライブラリのクイックスタート](quickstarts/client-libraries.md)
