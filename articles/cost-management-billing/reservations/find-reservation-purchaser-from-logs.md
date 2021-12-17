---
title: Azure Monitor のログから予約購入者を見つける
description: この記事は、Azure Monitor のログの情報を使用して予約購入者を見つけるのに役立ちます。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: baf7fe1968f2ba2a72e27f1caf85d02c65a3e93f
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514755"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Azure のログから予約購入者を見つける

この記事は、ディレクトリ ログの情報を使用して予約購入者を見つけるのに役立ちます。 Azure Monitor のディレクトリ ログには、予約購入を行ったユーザーの電子メール ID が示されています。

## <a name="find-the-purchaser"></a>購入者を見つける

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[監視]**  >  **[動作状況ログ]**  >  **[アクティビティ]** に移動します。  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="[動作状況ログ] - [アクティビティ] へのナビゲーションを示すスクリーンショット。" lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. **[ディレクトリ アクティビティ]** を選択します。 "*You need permission to view directory-level logs (ディレクトリレベルのログを表示するためのアクセス許可が必要です)* " というメッセージが表示された場合は、この [リンク](../../role-based-access-control/elevate-access-global-admin.md)を選択して、アクセス許可を取得する方法を確認してください。  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="ログを表示するアクセス許可のないディレクトリ アクティビティを示すスクリーンショット。" lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. アクセス許可を取得したら、 **[Tenant Resource Provider]\(テナントのリソース プロバイダー\)** を **Microsoft.Capacity** でフィルター処理します。 選択した期間のすべての予約関連イベントが表示されます。 必要に応じて、期間を変更します。  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="予約を購入したユーザーを示すスクリーンショット。" lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    必要に応じて、**列を編集** して、 **[イベント開始者]** を選択します。
   予約購入を行ったユーザーは、 **[イベント開始者]** の下に表示されます。

## <a name="next-steps"></a>次の手順

- 課金管理者は、必要に応じて、[予約の所有権を取得](view-reservations.md#view-and-manage-reservations)できます。
