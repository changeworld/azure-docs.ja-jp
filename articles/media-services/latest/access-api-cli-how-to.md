---
title: Azure Media Services API にアクセスする - CLI 2.0 | Microsoft Docs
description: Azure Media Services API にアクセスするには、このハウツー記事の手順に従います。
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 9295c3f9dfabc8ef7749758e926df443843720a1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639777"
---
# <a name="access-azure-media-services-api-with-cli-20"></a>CLI 2.0 で Azure Media Services API にアクセスする
 
Azure Media Services API に接続するには、Azure AD サービス プリンシパル認証を使用する必要があります。 アプリケーションは、次のパラメーターを持つ Azure AD トークンを要求する必要があります。

* Azure AD テナント エンドポイント
* Media Services リソース URI
* REST Media Services のリソース URI
* Azure AD アプリケーションの値: クライアント ID とクライアント シークレット

この記事では、CLI 2.0 を使用して、Azure Media Services リソースにアクセスする Azure AD アプリケーションとサービス プリンシパルを作成し、Azure Media Services リソースにアクセスするために必要な値を取得する方法について説明します。

## <a name="prerequisites"></a>前提条件 

[このクイックスタート](create-account-cli-quickstart.md)の説明に従って、新しい Azure Media Services アカウントを作成します。

## <a name="log-in-to-azure"></a>Azure にログインする

[Azure portal](http://portal.azure.com) にログインし、以降の手順で示すように CLI コマンドを実行するために **CloudShell** を起動します。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>関連項目

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
