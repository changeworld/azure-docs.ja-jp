---
title: グループ メンバーシップの一覧を一括でダウンロードする - Azure Active Directory ポータル | Microsoft Docs
description: Azure 管理センターでユーザーを一括で追加します。
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d27f1182f7e7308a58b97705ef20d5f2a726eb7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400150"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Azure Active Directory でグループのメンバーを一括ダウンロードする

Azure Active Directory (Azure AD) ポータルを利用し、組織のグループ メンバーをコンマ区切り値 (CSV) ファイルに一括ダウンロードできます。 グループ メンバーシップ リストは、管理者とそれ以外のユーザー全員がダウンロードできます。

## <a name="to-bulk-download-group-membership"></a>グループ メンバーを一括ダウンロードするには

1. 組織のアカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーシップをダウンロードするグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで **[メンバーのダウンロード]** を選択し、グループ メンバーの一覧を含む CSV ファイルをダウンロードします。

   ![[メンバーのダウンロード] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>ダウンロードの状態を確認する

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認します。](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

グループ メンバーの一覧をダウンロードする各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 50 万人のメンバーの一覧をダウンロードできます。

## <a name="next-steps"></a>次のステップ

- [グループ メンバーを一括インポートする](groups-bulk-import-members.md)
- [グループ メンバーを一括削除する](groups-bulk-download-members.md)
