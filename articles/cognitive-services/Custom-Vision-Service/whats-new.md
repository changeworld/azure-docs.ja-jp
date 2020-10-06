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
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 019a9264beddafbd4585810967551e064592e94d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602525"
---
# <a name="whats-new-in-custom-vision"></a>Custom Vision の新機能

サービス内の新機能について説明します。 以下の項目には、リリース ノート、ビデオ、ブログの投稿、およびその他の種類の情報が含まれる可能性があります。 このページをブックマークして、常にサービスの最新情報を確認してください。

## <a name="july-2020"></a>2020 年 7 月

### <a name="role-based-access-control"></a>ロールベースのアクセス制御

* Custom Vision では、Azure リソースへの個々のアクセスを管理するための認可システムである、Azure のロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Custom Vision プロジェクトへのアクセスを管理する方法については、「[ロールベースのアクセス制御](./role-based-access-control.md)」を参照してください。

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