---
title: Face API サービスとは
titleSuffix: Azure Cognitive Services
description: このトピックでは、Face API サービスとそれに関連する用語について説明します。
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310371"
---
# <a name="what-is-the-face-api-service"></a>Face API サービスとは

Face API サービスは、画像やビデオに含まれている人の顔を分析するためのアルゴリズムを備えたクラウドベースのサービスです。 Face API には、属性による顔検出と顔認識の 2 つの主要な機能があります。

## <a name="face-detection"></a>顔検出

Face API は、画像内で最大 64 個の人の顔を高い顔位置精度で検出します。 画像はファイル (バイト ストリーム) または有効な URL で指定できます。

![概要 - 顔検出](./Images/Face.detection.jpg)

画像内での顔の位置を示す顔の四角形 (左、上、幅、高さ) が、検出された各顔と共に返されます。 オプションとして、ポーズ、性別、年齢、頭のポーズ、顔の毛、眼鏡などの顔関連の属性を抽出します。 詳しくは、「[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)」(顔 -検出) をご覧ください。

## <a name="face-recognition"></a>顔認識

人の顔を識別する機能は、セキュリティ、自然なユーザー インターフェイス、画像コンテンツの分析と管理、モバイル アプリ、ロボティクスなど、多くのシナリオで注目されています。 Face API サービスには、顔検証、似た顔の検索、顔のグループ化、人物の特定の 4 つの顔認識機能があります。

### <a name="face-verification"></a>顔検証

顔検証は、検出された 2 つの顔に対する認証、または検出された 1 つの顔から 1 つのperson オブジェクトに対する認証を実行します。 詳しくは、「[Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)」(顔 - 検証) をご覧ください。

### <a name="finding-similar-faces"></a>似た顔の検索

ターゲットの検出された顔と、検索対象の顔の候補のセットを提供すると、サービスはターゲットの顔に最もよく似た少数の顔のセットを検索します。 **matchFace** と **matchPerson** の 2 つの動作モードがサポートされています。 **matchPerson** モードは、[顔 - 検証](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)で得られた同一人物のしきい値を適用した後で、似た顔を返します。 **matchFace** モードは、同一人物のしきい値を無視して、上位の似た顔候補を返します。 次の例における候補の顔のリストは次のとおりです。
![概要 - 似た顔の検索](./Images/FaceFindSimilar.Candidates.jpg) そして次の顔を照会します。
![概要 - 似た顔の検索](./Images/FaceFindSimilar.QueryFace.jpg)

4 つの似た顔を検索した場合、**matchPerson** モードは照会している顔と同じ人を表す (a) と (b) を返します。 **matchFace** モードは、類似性が低くても厳密に 4 つの候補を返すので、(a)、(b)、(c)、(d) が返されます。 詳しくは、「[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)」(顔 -類似の検索) をご覧ください。

### <a name="face-grouping"></a>顔のグループ化

不明な顔のセットを与えられると、顔グループ化 API は類似性に基づいて顔を複数のグループに自動的に分けます。 各グループは元の不明な顔セットの切り離された適切なサブセットであり、似た顔が含まれます。 同じグループ内のすべての顔は、同じ人物に属するものと見なすことができます。 詳しくは、「[Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)」(顔 -グループ) をご覧ください。

### <a name="person-identification"></a>人物の特定

Face API を使うと、検出された顔と人データベースを元にして人を識別できます。 このデータベースは事前に作成しておき、後で編集できます。

次の図は、"myfriends" という名前のデータベースの例です。 各グループは、最大で 1,000,000/10,000 個の異なる person オブジェクトを含むことができます。 各 person オブジェクトには最大で 248 個の顔を登録できます。

![概要 - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

データベースを作成してトレーニングした後、新しく検出された顔を含んだグループに対して識別を実行できます。 顔がグループ内の person として識別された場合、その person オブジェクトが返されます。

人の識別について詳しくは、以下の API ガイドをご覧ください。

[Face - 識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[PersonGroup - 作成](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[PersonGroup Person - 作成](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[PersonGroup - トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[LargePersonGroup - 作成](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[LargePersonGroup Person - 作成](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[LargePersonGroup - トレーニング](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Face ストレージと価格

Face ストレージを使用すると、Face API での識別または類似性照合のために LargePersonGroup/PersonGroup Person オブジェクト ([PersonGroup Person - 顔の追加](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person - 顔の追加](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) または LargeFaceLists/FaceLists ([FaceList - 顔の追加](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - 顔の追加](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) を使うときに、Standard サブスクリプションに顔を追加して保存できます 保存した画像には、顔 1,000 個あたり 0.50 ドルの料金がかかります。この料金は日割りで課金されます。 Free レベルのサブスクリプションは、合計で 1,000 人の制限があります。

たとえば、アカウントで月の前半は毎日 10,000 個の顔を保存し、後半は保存を行わなかった場合、保存した期間の 10,000 個の顔についてのみ料金が請求されます。 または、ある月に毎日数時間 1,000 個の顔を保存し、その日の夜にはそれらの顔を削除するとします。この場合でも、毎日 1,000 個の保存した顔について料金が請求されます。

## <a name="sample-apps"></a>サンプル アプリ

Face API を利用している以下のサンプル アプリケーションをご覧ください。

- [Microsoft Face API: Windows クライアント ライブラリとサンプル](https://github.com/Microsoft/Cognitive-Face-Windows)
  - 顔の検出、分析、識別の複数のシナリオを示す WPF サンプル アプリです。
- [FamilyNotes UWP アプリ](https://github.com/Microsoft/Windows-appsample-familynotes)
  - 家族ノート共有シナリオを使って音声、Cortana、インク、カメラの使い方を示すユニバーサル Windows プラットフォーム (UWP) サンプル アプリです。
- [ビデオ フレーム分析サンプル](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - Face API、Computer Vision API、Emotion API を使ってほぼリアルタイムでライブ ビデオ ストリームの分析を行う Win32 サンプル アプリです。

## <a name="tutorials"></a>チュートリアル
次のチュートリアルを見ると、Face API の基本機能とサブスクリプションのプロセスがわかります。
- [C# での Face API の使用開始チュートリアル](Tutorials/FaceAPIinCSharpTutorial.md)
- [Java for Android での Face API の使用開始チュートリアル](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [Python での Face API の使用開始チュートリアル](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>次の手順

簡単な Face API シナリオを実装するクイック スタートをお試しください。
- [クイック スタート: C# を使って画像の中にある顔を検出する](quickstarts/csharp.md) (その他の言語版もあり)
