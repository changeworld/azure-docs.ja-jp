---
author: baanders
description: Azure Digital Twins 設定でのロールの割り当てを確認するためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92489042"
---
ロールの割り当てを正常に設定したことを確認するための 1 つの方法として、[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスのロールの割り当てを表示します。 Azure portal で Azure Digital Twins インスタンスに移動します ([Azure Digital Twins インスタンス](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)のページで確認するか、ポータルの検索バーで名前を検索することができます)。

次に、 *[アクセス制御 (IAM)] > [ロールの割り当て]* で、それに割り当てられているすべてのロールを表示します。 一覧に *"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)* のロールを持つユーザーが表示されます。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure portal での Azure Digital Twins インスタンスのロールの割り当てのビュー":::