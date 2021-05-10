---
title: Azure Content Moderator とは
titleSuffix: Azure Cognitive Services
description: ユーザーによって生成されたコンテンツに含まれる不適切な素材の追跡、フラグ設定、評価、フィルタリングを Content Moderator を使用して行う方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/16/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: コンテンツ モデレーター, azure コンテンツ モデレーター, オンライン モデレーター, コンテンツ フィルター ソフトウェア, コンテンツ モデレーション サービス, コンテンツ モデレーション
ms.openlocfilehash: b0ff5a241a76cd49d104e4145df37515b43c4e27
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726198"
---
# <a name="what-is-azure-content-moderator"></a>Azure Content Moderator とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator は、不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容を管理できる AI サービスです。 テキスト、画像、動画をスキャンし、コンテンツ フラグを自動的に適用する AI 搭載コンテンツ モデレーション サービスと、人間のレビュー担当者からなるチーム用のオンライン モデレーター環境であるレビュー ツールが含まれています。

法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込むことがあります。

このドキュメントには、次の種類の記事が含まれています。  

* [**クイックスタート**](client-libraries.md)は、サービスへの要求の実行方法を説明する概要手順です。  
* [**攻略ガイド**](try-text-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。  
* [**概念**](text-moderation-api.md)では、サービスの機能と特徴について詳しく説明します。  
* [**チュートリアル**](ecommerce-retail-catalog-moderation.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。  

## <a name="where-its-used"></a>使用場所

ソフトウェア開発者またはチームがコンテンツ モデレーション サービスを使用するシナリオをいくつか次に示します。

- ユーザーが生成した製品カタログなどのコンテンツをモデレートするオンライン マーケットプレース。
- ユーザーが生成したゲームの成果物とチャット ルームをモデレートするゲーム会社。
- ユーザーによって追加された画像、テキスト、ビデオをモデレートするソーシャル メッセージング プラットフォーム。
- コンテンツに対する一元的なモデレーションを実装するエンタープライズ メディア企業。
- 学生および教育者にとって不適切なコンテンツをフィルター処理して除外する K-12 教育ソリューション プロバイダー。

> [!IMPORTANT]
> Content Moderator を使用して、子どもの搾取に該当する違法な画像を検出することはできません。 ただし、認められた機関は [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") を使用してこのようなコンテンツを審査できます。

## <a name="what-it-includes"></a>備えている機能

Content Moderator サービスは、REST 呼び出しと .NET SDK のどちらを介しても利用できるいくつかの Web サービス API で構成されています。 ほかにはレビュー ツールが用意されており、レビュー担当者がサービスを補助したり、モデレーション機能を調整したりできるようになっています。

## <a name="moderation-apis"></a>モデレート API

Content Moderator サービスには、モデレート API が含まれています。これは、不適切または好ましくない可能性がある素材についてコンテンツをチェックします。

![Content Moderator モデレーション API のブロック図](images/content-moderator-mod-api.png)

次の表では、さまざまな種類のモデレーション API について説明します。

| API グループ | 説明 |
| ------ | ----------- |
|[**テキストのモデレーション**](text-moderation-api.md)| テキストをスキャンし、不適切な表現、露骨に性的な表現、性的な内容を暗示する表現、下品な表現、個人データが含まれているかどうかを確認します。|
|[**カスタム用語リスト**](try-terms-list-api.md)| テキストをスキャンし、組み込みの用語と共に、カスタムの用語リストと照合します。 カスタム リストは、独自のコンテンツ ポリシーに従ってコンテンツをブロックまたは許可する際に使用します。|  
|[**画像のモデレーション**](image-moderation-api.md)| 画像をスキャンして成人向けのコンテンツやわいせつなコンテンツが含まれているかどうかを確認するほか、光学式文字認識 (OCR) 機能により画像内のテキストを検出したり、顔検出を実行したりします。|
|[**カスタム画像リスト**](try-image-list-api.md)| 画像をスキャンし、画像のカスタム リストと照合します。 カスタム画像リストを使うと、何度も出現し、そのたびに分類をするのが煩わしいコンテンツを除去できます。|
|[**ビデオのモデレーション**](video-moderation-api.md)| ビデオをスキャンして成人向けのコンテンツやわいせつなコンテンツが含まれているかどうかを確認し、そのようなコンテンツがあればタイム マーカーを返します。|

## <a name="review-apis"></a>API の確認

Review API を使用すると、モデレーション パイプラインを人間のレビューアーと統合することができます。 [ジョブ](review-api.md#jobs)、[レビュー](review-api.md#reviews)、[ワークフロー](review-api.md#workflows)の操作を使用して、[レビュー ツール](#review-tool) (下記参照) を使った人間参加型のワークフローを作成したり、自動化したりできます。

> [!NOTE]
> Workflow API は、.NET SDK ではまだ利用できませんが、REST エンドポイントで使用できます。

![Content Moderator レビュー API のブロック図](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>レビュー ツール

Content Moderator サービスには、ほかにも Web ベースの[レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md)が備わっています。このツールは、ヒューマン モデレーターによって処理されるコンテンツ レビューのホストとなります。 人間による入力がサービスに対するトレーニングになることはありませんが、このサービスの成果と目視レビュー チームの作業を組み合わせれば、開発者が効率性と正確性をうまく両立させることが可能になります。 また、レビュー ツールは、いくつかの Content Moderator リソース向けに、ユーザー フレンドリなフロントエンドも備えています。

![Content Moderator レビュー ツール ホームページ](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Content Moderator サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

Web ポータルで Content Moderator の使用を開始するには、「[Web 上で Content Moderator を試す](quick-start.md)」の手順に従います。 または、[クライアント ライブラリや REST API クイックスタート](client-libraries.md)を完了し、コードに基本シナリオを実装します。