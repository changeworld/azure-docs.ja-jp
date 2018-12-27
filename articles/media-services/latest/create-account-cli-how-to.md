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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: cb49b642137517c0ceef7d2fa01994a554db1f4e
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613161"
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services アカウントの作成

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントを作成するときは、同時に Media Services アカウントと同じリージョンにストレージ アカウントも作成して関連付けます (または既存のストレージ アカウントを使用します)。

この記事では、Azure CLI を使用して、新しい Azure Media Services アカウントを作成する手順について説明します。  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- 有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

次のコマンドで、Media Services アカウントで使用する Azure サブスクリプション ID を指定します。 [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動することで、アクセス権があるサブスクリプションの一覧を表示できます。

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

