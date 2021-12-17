---
title: Azure Face サービスとは
titleSuffix: Azure Cognitive Services
description: Azure Face サービスは、画像に含まれている人の顔を検出、認識、分析するために使用する AI アルゴリズムを提供します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 顔認識, 顔認識ソフトウェア, 顔分析, 顔照合, 顔認識アプリ, 画像による顔検索, 顔認識検索
ms.openlocfilehash: 370d6c17dfaf2d3544506e4e2ab4218f1a48d1fa
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370804"
---
# <a name="what-is-the-azure-face-service"></a>Azure Face サービスとは

> [!WARNING]
> 2020 年 6 月 11 日に、Microsoft は、人権に基づく厳格な法令が制定されない限り、米国内の警察に顔認識テクノロジを販売しないことを発表しました。 このため、顧客は、米国内の警察である場合、または警察による顔認識機能および Azure サービスに含まれる機能 (Face や Video Indexer など) の使用を許可する場合、これらの機能を使用できません。 新しい Face リソースを作成する際には、Azure portal で、サービスを米国の警察で、もしくは警察のために使用しないこと、および[責任ある AI (RAI) のドキュメント](../cognitive-services-apis-create-account-cli.md#prerequisites)を確認し、それに従ってこのサービスを使用することに同意する必要があります。

Azure Face サービスは、画像に含まれている人の顔の検出、認識、分析する AI アルゴリズムを提供します。 顔認識ソフトウェアは、本人確認、タッチレス アクセス制御、プライバシーのための顔ぼかしなど、さまざまなシナリオで重要です。

本人確認: 政府機関が発行した ID カード (パスポート、運転免許証、その他の登録イメージなど) に対してユーザーがその本人であるかを検証し、デジタルまたは物理サービスへのアクセスを許可したり、アカウントを回復したりします。 特定のアクセス シナリオには、新しいアカウントの開設、従業員の確認、オンライン評価の管理が含まれます。 本人確認は、人物がオンボードされるときに 1 回実行したり、誰かがデジタル サービスまたは物理サービスにアクセスするたびに繰り返し実行したりできます。

タッチレス アクセス制御: カードやチケットのような今日の方法と比較した場合、オプトインの顔識別を使用すると、カードの共有、損失、盗難による衛生上ならびにセキュリティ上のリスクを軽減しながら、アクセス制御エクスペリエンスを向上できます。 顔認識は、空港、スタジアム、テーマパーク、建物でのチェックインだけでなく、オフィス、病院、ジム、クラブ、学校の受付キオスクのための、人間が介入するチェックイン プロセスを支援します。

顔編集: プライバシーを保護するため、ビデオに記録された人の顔を編集またはぼかします。

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./Quickstarts/client-libraries.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。 
* [攻略ガイド](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [概念の記事](./concepts/face-detection.md)では、サービスの機能と特長について詳しく説明します。
* [チュートリアル](./enrollment-overview.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

## <a name="face-detection-and-analysis"></a>顔検出と分析

顔検出は、他のすべてのシナリオの最初の手順として必要です。 Detect API では、画像に含まれている人の顔を検出し、その位置の四角形の座標を返します。 また、保存された顔データを表す一意の ID も返されます。これは、後の操作で顔を識別または検証するために使用されます。

顔検出では、オプションとして、頭部姿勢、年齢、感情、ひげ、眼鏡などの顔関連の属性を抽出することもできます。 これらの属性はおおよその予測であって、実際の分類ではありません。 一部の属性は、ユーザーが Face サービスに自分自身を追加する際に、アプリケーションが高品質の顔データを取得できるようにするために役立ちます (たとえば、ユーザーがサングラスをかけている場合に、ユーザーにサングラスを外すように伝えるなど)。

> [!NOTE]
> 顔検出機能は [Computer Vision サービス](../computer-vision/overview.md)でもご利用いただけます。 ただし、その他の Face 操作 （Identify、Verify、Find Similar、または Face グループ化など) を実行したい場合は、代わりにこのサービスをご利用いただく必要があります。

顔検出と分析の詳細については、[顔検出](concepts/face-detection.md)の概念に関する記事を参照してください。 また、[Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) リファレンス ドキュメントも参照してください。


## <a name="identity-verification"></a>本人確認

最新技術を使用する企業やアプリでは、Face 識別操作と Face 検証操作を使用して、ユーザーの身元を確認できます。 

### <a name="identification"></a>識別

顔識別では、画像内の 1 つの顔を、安全なリポジトリ内の一連の顔と "一対多" で照合できます。 一致候補は、顔データがクエリの顔とどれだけ一致しているかに基づいて返されます。 このシナリオは、特定のユーザー グループに建物や空港へのアクセス権を付与したり、デバイスのユーザーを検証したりするために使用されます。

次の画像は、`"myfriends"` という名前のデータベースの例を示しています。 各グループは、最大で 100 万個の異なる person オブジェクトを含むことができます。 各 person オブジェクトには最大で 248 個の顔を登録できます。

![それぞれ異なる人物に対応する 3 つの列とそれぞれ 3 行の顔の画像から成るグリッド](./Images/person.group.clare.jpg)

グループを作成してトレーニングした後、新しく検出された顔のグループに対して識別を実行できます。 顔がグループ内の person として識別された場合、その person オブジェクトが返されます。

### <a name="verification"></a>検証

検証操作は、"これら 2 つの顔は同じ人物のものでしょうか?" という質問に答えます。 

また、検証では、画像内の顔をセキュリティで保護されたリポジトリや写真からの 1 つの顔と "1 対 1" で照合して、それらが同じ個人であることが確認されます。 検証は、ユーザーが自分の身元を確認するために自撮りしたり、自分の写真付き ID を撮影することで、リモートでクレジット アカウントを開くことができる銀行アプリなどの本人確認に使用することができます。 

本人確認の詳細については、[顔認識](concepts/face-recognition.md)の概念ガイドまたは [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) および [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) の API リファレンス ドキュメントを参照してください。


## <a name="find-similar-faces"></a>似た顔の検索

Find Similar 操作では、ターゲットの顔と候補となる一連の顔との間で顔照合を行い、ターゲットの顔によく似ている一連の顔が検索されます。 これは、画像による顔検索を行う場合に便利です。 

このサービスでは、**matchPerson** と **matchFace** の 2 つの動作モードがサポートされています。 **matchPerson** モードでは、[Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) を使用して同一人物についてフィルター処理が行われた後、似た顔が返されます。 **matchFace** モードでは、同一人物フィルターは無視されます。 同一人物のものであるかどうかに関係なく、似ている顔の候補のリストが返されます。

ターゲットの顔の例を次に示します。

![笑顔の女性](./Images/FaceFindSimilar.QueryFace.jpg)

そして候補となる顔の画像は次のとおりです。

![笑っている人々の 5 つの画像。 A と B の画像は同じ人を示しています。](./Images/FaceFindSimilar.Candidates.jpg)

4 つの似た顔を検索する場合、**matchPerson** モードではターゲットの顔と同じ人を表す A とB が返されます。 **matchFace** モードでは、ターゲットと同一人物ではない場合や類似性が低い場合でも、厳密に 4 つの候補が返されるので、A、B、C、D が返されます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) リファレンス ドキュメントを参照してください。

## <a name="group-faces"></a>顔をグループ化する

Group 操作では、未知の顔の集合が、類似性に基づいて複数のグループに分けられます。 それぞれのグループは、元の顔の集合から得られる、互いに素な真部分集合です。 また、類似点が見つからなかった顔の ID を含む、単一の "messyGroup" 配列も返されます。

返されたグループに含まれるすべての顔は同一人物のものである可能性が高いものの、1 人の人物について、いくつかの異なるグループが存在することがあります。 これらのグループは、たとえば表情など、別の要因によって区別されます。 詳細については、[顔認識](concepts/face-recognition.md)の概念のガイドまたは [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) リファレンス ドキュメントを参照してください。

## <a name="sample-app"></a>サンプル アプリ

以下のサンプル アプリケーションは、Face サービスの使用方法をいくつか示しています。

- [FamilyNotes UWP アプリ](https://github.com/Microsoft/Windows-appsample-familynotes)は、家族でノートを共有するシナリオにおいて、音声、Cortana、インク、カメラと共に顔の識別が使用されるユニバーサル Windows プラットフォーム (UWP) アプリです。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services リソース全般に言えることですが、Face サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

クイックスタートに従って、顔認識アプリの基本コンポーネントを任意の言語でコーディングします。

- [クライアント ライブラリのクイックスタート](quickstarts/client-libraries.md)
