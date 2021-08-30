---
title: Azure Media Services v3 アカウントの管理
description: Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 この記事では、Azure Media Services v3 アカウントを管理する方法について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: cbda31a3aaaf741424db174aa6a676858b27a23c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471214"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 アカウントの管理

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 詳細については、[ストレージ アカウント](storage-account-concept.md)に関するページを参照してください。

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="media-services-account-names"></a>Media Services アカウント名

Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。 Media Services アカウント名は、Azure の場所内で一意である必要があります。

Media Services アカウントが削除されると、アカウント名は 1 年間予約されます。 アカウントが削除された 1 年後、アカウント名は、元のアカウントが含まれていたサブスクリプションによって同じ Azure の場所でのみ再利用できます。

Media Services アカウント名は、キー配信、ライブ イベント、ストリーミング エンドポイント名など、DNS 名に使用されます。 Media Services DNS 名を許可するようにファイアウォールまたはプロキシを構成している場合、Media Services アカウントを削除してから 1 年以内にこれらの構成が削除されていることを確認してください。

## <a name="moving-a-media-services-account-between-subscriptions"></a>サブスクリプション間での Media Services アカウントの移動

Media Services アカウントを新しいサブスクリプションに移動する必要がある場合は、まず、Media Services アカウントを含むリソース グループ全体を新しいサブスクリプションに移動する必要があります。 接続されている次のすべてのリソースを移動する必要があります。Azure Storage アカウント、Azure CDN プロファイルなど。詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。 Azure の他のリソースの場合と同様に、リソース グループの移動にはその完了まで時間がかかることがあります。

### <a name="considerations"></a>考慮事項

* 別のサブスクリプションに移行する前に、ご利用のアカウント内のすべてのデータのバックアップを作成します。
* ストリーミング エンドポイントとライブ ストリーミング リソースをすべて停止する必要があります。 リソース グループの移動中、ユーザーはコンテンツにアクセスできません。 

> [!IMPORTANT]
> 移動が正常に完了するまで、ストリーミング エンドポイントを開始しないでください。

### <a name="troubleshoot"></a>トラブルシューティング

リソース グループの移動後に、Media Services アカウントまたは関連付けられた Azure Storage アカウントが "切断" される場合、ストレージ アカウント キーの交換を試してください。 ストレージ アカウント キーの交換で Media Services アカウントの "切断" 状態が解決されない場合は、Media Services アカウントの [サポート + トラブルシューティング] メニューから新しいサポート リクエストを提出してください。  

## <a name="next-steps"></a>次のステップ

[アカウントの作成](./account-create-how-to.md)
