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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 54a92e56df21b59430ed12f191a9cf7a918e14c9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612901"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Azure CLI で Azure Media Services API にアクセスする
 
Azure Media Services API に接続するには、Azure AD サービス プリンシパル認証を使用する必要があります。 アプリケーションは、次のパラメーターを持つ Azure AD トークンを要求する必要があります。

* Azure AD テナント エンドポイント
* Media Services リソース URI
* REST Media Services のリソース URI
* Azure AD アプリケーションの値: クライアント ID とクライアント シークレット

この記事では、Azure CLI を使用して、Azure AD アプリケーションとサービス プリンシパルを作成し、Azure Media Services リソースにアクセスするために必要な値を取得する方法について説明します。

## <a name="prerequisites"></a>前提条件 

- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

- [Media Services アカウントを作成する](create-account-cli-how-to.md)

    Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
