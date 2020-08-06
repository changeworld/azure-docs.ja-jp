---
author: baanders
description: Azure Digital Twins 設定でのロールの割り当てを確認するためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405597"
---
ロールの割り当てを正常に設定したことを確認するための 1 つの方法として、[Azure portal](https://portal.azure.com) で Azure Digital Twins インスタンスのロールの割り当てを表示します。 [Azure Digital Twins インスタンス](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)のポータルのページ (このリンクを使用するか、またはポータルの検索バーで検索できます) に移動し、確認するインスタンスの名前を選択します。 

次に、 *[アクセス制御 (IAM)] > [ロールの割り当て]* で、それに割り当てられているすべてのロールを表示します。 ユーザーは、 *[Azure Digital Twins 所有者 (プレビュー)]* のロールで一覧に表示されます。 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Azure portal での Azure Digital Twins インスタンスのロールの割り当てのビュー":::