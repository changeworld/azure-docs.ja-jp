---
title: Azure Active Directory ポータルでグループの一覧をダウンロードする | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでグループ プロパティを一括ダウンロードします。
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
ms.openlocfilehash: 298e961eff1736f32d2fa703dfb0079ea758e343
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401385"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Azure Active Directory でグループの一覧を一括ダウンロードする

Azure Active Directory (Azure AD) ポータルを利用し、組織の全グループの一覧をコンマ区切り値 (CSV) ファイルに一括ダウンロードできます。 グループ リストは、管理者とそれ以外のユーザー全員がダウンロードできます。

## <a name="to-download-a-list-of-groups"></a>グループの一覧をダウンロードするには

1. 組織のアカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. Azure AD で、 **[グループ]** 、 **[グループのダウンロード]** の順に選択します。
1. **[グループのダウンロード]** ページで **[開始]** を選択すると、グループの一覧を含む CSV ファイルが得られます。

   ![[グループのダウンロード] コマンドは [すべてのグループ] ページにあります。](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>ダウンロードの状態を確認する

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認します。](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

グループの一覧を一括ダウンロードするごとに、最大 1 時間かかることがあります。 少なくとも 300,000 グループの一覧を一括でダウンロードできます。

## <a name="next-steps"></a>次のステップ

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
