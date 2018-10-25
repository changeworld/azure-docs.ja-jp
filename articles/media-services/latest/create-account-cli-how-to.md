---
title: Azure CLI での Azure Media Services アカウントの作成 | Microsoft Docs
description: Azure Media Services アカウントを作成するには、このクイック スタートの手順に従います。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 988b56569c2537f9f0c74b1bcc2bf5e2a9400a80
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378807"
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services アカウントの作成

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントを作成するときは、同時に Media Services アカウントと同じリージョンにストレージ アカウントも作成して関連付けます (または既存のストレージ アカウントを使用します)。

この記事では、Azure CLI を使用して、新しい Azure Media Services アカウントを作成する手順について説明します。  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](http://portal.azure.com) にサインインし、以降の手順で示すように CLI コマンドを実行するために **CloudShell** を起動します。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

次のコマンドで、Media Services アカウントで使用する Azure サブスクリプション ID を指定します。 [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動することで、アクセス権があるサブスクリプションの一覧を表示できます。

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
