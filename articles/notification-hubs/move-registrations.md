---
title: リージョン間で Azure Notification Hubs リソースを移動する
description: Azure Notification Hubs リソースを別の Azure リージョンに移動する方法について説明します。
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: how-to
ms.date: 09/07/2021
ms.custom: template-how-to
ms.openlocfilehash: ae1f52ea007746ab162f13edf77f34fe8c8157c1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004988"
---
# <a name="move-resources-between-azure-regions"></a>Azure リージョン間でリソースを移動する

この記事では、Azure Notification Hubs リソースを別の Azure リージョンに移動する方法について説明します。 このプロセスの概要は次のとおりです。

1. 別の名前を使用してターゲットの名前空間を作成します。
1. 前の名前空間から登録をエクスポートします。
1. 目的のリージョンの新しい名前空間に登録をインポートします。

## <a name="overview"></a>概要

シナリオによっては、さまざまなビジネス上の理由により、Azure リージョン間でサービス リソースを移動することが必要になります。 新しく利用可能なリージョンに移動する場合があります。特定のリージョンでのみ利用可能な機能やサービスをデプロイしたり、内部ポリシーやコンプライアンスの要件が原因で移動したり、容量の問題を解決したりすることができます。

Azure Notification Hubs 名前空間の名前は一意であり、登録はハブごとに行われます。そのため、このような移動を実行するには、目的のリージョンに新しいハブを作成し、その他すべての関連データと共に、新しく作成した名前空間に登録を移動する必要があります。

## <a name="create-a-notification-hubs-namespace-with-a-different-name"></a>別の名前を使用して Notification Hubs 名前空間を作成する

新しい Notification Hubs 名前空間を作成するには、次の手順に従います。 名前空間の目的の宛先リージョンを含む、必要なすべての情報を **[基本]** タブに入力します。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

新しい名前空間を作成したら、必ず、新しい名前空間で PNS の資格情報を設定し、新しい名前空間に同等のポリシーを作成してください。

## <a name="exportimport-registrations"></a>登録のエクスポート/インポートを行う

リソースの移動先となるリージョンに新しい名前空間が作成されたら、すべての登録を一括してエクスポートし、新しい名前空間にインポートします。 そのためには、「[Azure Notification Hubs の登録情報を一括でエクスポートおよびインポートする](export-modify-registrations-bulk.md)」を参照してください。

## <a name="delete-the-previous-namespace-optional"></a>前の名前空間を削除する (省略可能)

前の名前空間から新しい名前空間への登録のエクスポートが完了したら、必要に応じて前の名前空間を削除できます。

1. 前のリージョンの既存の名前空間にアクセスします。

2. **[削除]** を選択して、 **[名前空間の削除]** ウィンドウで名前空間名を再入力します。

3. **[名前空間の削除]** ウィンドウの下部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

次の記事は、リージョンの移動に関する記事がある他のサービスの例です。

- [NSG を別のリージョンに移動する](../virtual-network/move-across-regions-nsg-portal.md)
- [パブリック IP アドレスを別のリージョンに移動する](../virtual-network/move-across-regions-publicip-portal.md)
- [ストレージ アカウントを別のリージョンに移動する](../storage/common/storage-account-move.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [(リソース グループ) のリソースをリージョン間で移動する](../resource-mover/move-region-within-resource-group.md)
