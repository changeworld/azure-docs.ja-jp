---
title: Azure CLI の例 - Azure Media Services | Microsoft Docs
description: Azure Media Services 用の Azure CLI の例
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109250"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure Media Services 用の Azure CLI の例

次の表に、Azure Media Services 用の Azure CLI の例へのリンクが含まれています。

## <a name="examples"></a>例

|  |  |
|---|---|
|**スケール**||
| [メディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)|Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 の MRU でプロビジョニングすることを強くお勧めします。 <br/>このスクリプトは、CLI を使用してメディア占有ユニット (MRU) をスケーリングする方法を示します。|
|**アカウント**||
| [Media Services アカウントを作成する](./scripts/cli-create-account.md) | Azure Media Services アカウントを作成します。 プログラムでアカウントを管理する API にアクセスするために使用できるサービス プリンシパルも作成できます。 |
| [アカウントの資格情報をリセットする](./scripts/cli-reset-account-credentials.md)|アカウントの資格情報をリセットし、app.config settings 設定を元に戻します。|
|**資産**||
| [資産を作成する](./scripts/cli-create-asset.md)|コンテンツをアップロードする Media Services Asset を作成します。|
| [ファイルをアップロードする](./scripts/cli-upload-file-asset.md)|ローカル ファイルをストレージ コンテナーにアップロードします。|
| **トランスフォーム**と**ジョブ**||
| [トランスフォームを作成する](./scripts/cli-create-transform.md)|トランスフォームの作成方法を示します。 トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。<br/> 必要な名前と "レシピ" を持つトランスフォームが既に存在するかどうかを必ず確認する必要があります。 該当する場合は、それを再利用します。 |
| [ジョブを作成する](./scripts/cli-create-jobs.md)|HTTPS URL を使用する単純なエンコードされたトランスフォームにジョブを送信します。|
| [EventGrid を作成する](./scripts/cli-create-event-grid.md)|ジョブ状態変更用のアカウント レベルの Event Grid サブスクリプションを作成します。|
| **配信する**||
| [資産を公開する](./scripts/cli-publish-asset.md)| ストリーミング ロケーターを作成し、ストリーミング URL を戻します。 |
| [Filter](filters-dynamic-manifest-cli-howto.md)| ビデオ オンデマンド資産のためのフィルターを構成し、CLI を使用して[アカウント フィルター](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)と[資産フィルター](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)を作成する方法を示します。 

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
