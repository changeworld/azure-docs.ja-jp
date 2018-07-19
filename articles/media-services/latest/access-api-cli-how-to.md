---
title: Azure Media Services API にアクセスする - Azure CLI | Microsoft Docs
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
ms.openlocfilehash: e20cac5f1063589bdbfee0f384ac6af5a39811ed
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723694"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Azure CLI で Azure Media Services API にアクセスする
 
Azure Media Services API に接続するには、Azure AD サービス プリンシパル認証を使用する必要があります。 アプリケーションは、次のパラメーターを持つ Azure AD トークンを要求する必要があります。

* Azure AD テナント エンドポイント
* Media Services リソース URI
* REST Media Services のリソース URI
* Azure AD アプリケーションの値: クライアント ID とクライアント シークレット

この記事では、Azure CLI を使用して、Azure AD アプリケーションとサービス プリンシパルを作成し、Azure Media Services リソースにアクセスするために必要な値を取得する方法について説明します。

## <a name="prerequisites"></a>前提条件 

[このクイックスタート](create-account-cli-quickstart.md)の説明に従って、新しい Azure Media Services アカウントを作成します。

## <a name="log-in-to-azure"></a>Azure にログインする

[Azure portal](http://portal.azure.com) にログインし、以降の手順で示すように CLI コマンドを実行するために **CloudShell** を起動します。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
