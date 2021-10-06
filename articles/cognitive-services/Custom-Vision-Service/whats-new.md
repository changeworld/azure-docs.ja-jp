---
title: Custom Vision の新機能
titleSuffix: Azure Cognitive Services
description: この記事には、Custom Vision に関するニュースが含まれています。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.openlocfilehash: 677a5ff8214c8f43ec5dfe1d6d6e73b1d03b2650
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356952"
---
# <a name="whats-new-in-custom-vision"></a>Custom Vision の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。


## <a name="october-2020"></a>2020 年 10 月 

### <a name="custom-base-model"></a>カスタムの基本モデル

- 一部のアプリケーションには、大量の共同トレーニング データがありますが、それらのモデルは個別に微調整する必要があります。これにより、異なるソースからの若干の差異があるイメージのパフォーマンスを向上させることができます。 この場合、大量のトレーニング データを使用して、最初のモデルを通常どおりトレーニングできます。 その後、要求本文で _CustomBaseModelInfo_ を使用して 3.4 パブリック プレビュー API の **TrainProject** を呼び出して、ダウンストリーム プロジェクトの基本モデルとして、この最初の段階のトレーニング済みモデルを使用します。 ソース プロジェクトとダウンストリーム ターゲット プロジェクトでイメージ特性が類似している場合は、パフォーマンスの向上を期待できます。 

### <a name="new-domain-information"></a>新しいドメインの情報

- Custom Vision 3.4 パブリック プレビュー API の **GetDomains** から返されるドメイン情報に、サポートされるエクスポート可能なプラットフォーム、モデル アーキテクチャの簡単な説明、およびコンパクト ドメイン用のモデルのサイズが含まるようになりました。

### <a name="training-divergence-feedback"></a>トレーニングの相違に関するフィードバック

- Custom Vision 3.4 パブリック プレビュー API で、**GetIteration** の呼び出しから **TrainingErrorDetails** が返されるようになりました。 反復が失敗した場合、これにより、トレーニングの相違が原因で失敗したかどうかが分かります。これは、より多くの高品質なトレーニング データを使用して修正できます。

## <a name="july-2020"></a>2020 年 7 月

### <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

* Custom Vision では、Azure リソースへの個々のアクセスを管理するための認可システムである、Azure のロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Custom Vision プロジェクトへのアクセスを管理する方法については、「[Azure ロールベースのアクセス制御](./role-based-access-control.md)」を参照してください。

### <a name="subset-training"></a>サブセット トレーニング

* 物体検出プロジェクトをトレーニングするときに、必要に応じて、適用したタグのサブセットのみでトレーニングできます。 ある特定のタグが十分に適用されていないが、他のタグが十分に適用されている場合に、これを行うことができます。 詳しくは、C# または Python の[クライアント ライブラリのクイックスタート](./quickstarts/object-detection.md)に従ってください。

### <a name="azure-storage-notifications"></a>Azure ストレージ通知

* Custom Vision プロジェクトを Azure Blob Storage キューと統合して、プロジェクトのトレーニングのプッシュ通知を取得したり、公開されたモデルのアクティビティ コピーやバック アップコピーをエクスポートしたりすることができます。 この機能は、長い操作が実行されているときに、サービスで結果をポーリングし続けなくて済むようにするのに役立ちます。 代わりに、ストレージ キュー通知をワークフローに統合できます。 詳細については、[ストレージの統合](./storage-integration.md)ガイドを参照してください。

### <a name="copy-and-move-projects"></a>プロジェクトをコピーおよび移動する

* プロジェクトを 1 つの Custom Vision アカウントから別のものにコピーできるようになりました。 プロジェクトを開発環境から運用環境に移動することや、データのセキュリティを強化するために別の Azure リージョンのアカウントにプロジェクトをバックアップすることが必要になる場合があります。 詳細については、[プロジェクトのコピーと移動](./copy-move-projects.md)のガイドを参照してください。

## <a name="september-2019"></a>2019 年 9 月

### <a name="suggested-tags"></a>推奨されるタグ

* [Custom Vision Web サイト](https://www.customvision.ai/)の Smart Labeler ツールでは、トレーニング画像用の推奨タグが生成されます。 これにより、Custom Vision モデルをトレーニングするときに、多数の画像にすばやくラベルを付けることができます。 この機能の使用方法については、[推奨されるタグ](./suggested-tags.md)に関するページをご覧ください。

## <a name="cognitive-service-updates"></a>Cognitive Services の更新プログラム

[Cognitive Services に対する Azure 更新プログラムのお知らせ](https://azure.microsoft.com/updates/?product=cognitive-services)