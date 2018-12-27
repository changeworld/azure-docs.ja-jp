---
title: メディア占有ユニットのスケーリング - Azure |Microsoft Docs
description: このトピックでは、Azure Media Services を使用したメディア処理のスケール設定の概要を示します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: db1915f23c33b5cc0d504f8fcc21b9533228247f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634398"
---
# <a name="scaling-media-processing"></a>メディア処理のスケーリング

Azure Media Services を使用してメディア占有ユニット (MRU) を管理することで、お使いのアカウントで実行されるメディア処理をスケーリングできます。 詳細については、「[メディア処理のスケール設定の概要](../previous/media-services-scale-media-processing-overview.md)」を参照してください。 この記事では、[Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) を使用して MRU をスケーリングする方法を示します。

> [!IMPORTANT]
> [このセクション](#considerations)で説明する考慮事項を確認してください。  
> 
>

## <a name="prerequisites"></a>前提条件 

- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](create-account-cli-how-to.md)

## <a name="scale-media-reserved-units-with-cli"></a>CLI を使用してメディア占有ユニットをスケーリングする

次の [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) コマンドでは、"amsaccount" アカウントのメディア占有ユニットを、**count** パラメーターと **type** パラメーターを使用して設定しています。

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="considerations"></a>考慮事項

- Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 の MRU でプロビジョニングすることを強くお勧めします。
- 10 個を超える S3 の MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。

## <a name="billing"></a>課金

お客様のアカウントにプロビジョニングされた MRU の数、種類、および 時間に基づいて課金されます。 料金はジョブの実行の有無に関係なく適用されます。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="next-step"></a>次のステップ

[ビデオを分析する](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
