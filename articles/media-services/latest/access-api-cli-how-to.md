---
title: Azure Media Services API にアクセスする - Azure CLI | Microsoft Docs
description: Azure Media Services API にアクセスするには、このハウツー記事の手順に従います。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 06ca26ee9a8af7f49aba6a48a1831d30d5886851
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896330"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Azure CLI で Azure Media Services API にアクセスする
 
Azure AD サービス プリンシパル認証を使用して Azure Media Services API に接続するには、アプリケーションから、以下のパラメーターを使用して Azure AD トークンを要求する必要があります。

* Azure AD テナント エンドポイント
* Media Services リソース URI
* REST Media Services のリソース URI
* Azure AD アプリケーションの値: クライアント ID とクライアント シークレット

> [!IMPORTANT]
> 詳細については、[Media Services v3 API にアクセスする](media-services-apis-overview.md#accessing-the-azure-media-services-api)を参照してください。

この記事では、Azure CLI を使用して、Azure AD アプリケーションとサービス プリンシパルを作成し、Azure Media Services リソースにアクセスするために必要な値を取得する方法について説明します。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md)

Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>参照

- [メディア占有ユニットのスケーリング - CLI](media-reserved-units-cli-how-to.md)
- [Media Services アカウントを作成する - CLI](create-account-cli-how-to.md) 
- [アカウントの資格情報をリセットする - CLI](cli-reset-account-credentials.md)
- [資産を作成する - CLI](cli-create-asset.md)
- [ファイルをアップロードする - CLI](cli-upload-file-asset.md)
- [トランスフォームを作成する - CLI](cli-create-transform.md)
- [カスタム変換を使用してエンコードする - CLI](custom-preset-cli-howto.md)
- [ジョブを作成する - CLI](cli-create-jobs.md)
- [EventGrid を作成する - CLI](job-state-events-cli-how-to.md)
- [資産を公開する - CLI](cli-publish-asset.md)
- [フィルター - CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>次のステップ

コンテンツのストリーミング元のストリーミング エンドポイントは、実行中状態である必要があります。 次の CLI は、既定のストリーミング エンドポイントを開始します:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

