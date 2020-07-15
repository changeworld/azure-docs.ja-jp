---
title: Azure Active Directory ポータルでグループの一覧をダウンロードする | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでグループ プロパティを一括ダウンロードします。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00fdd94e8a8cd4b8769260cd595dfee5ff898039
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732620"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Azure Active Directory でグループの一覧を一括ダウンロードする

Azure Active Directory (Azure AD) ポータルを利用し、組織の全グループの一覧をコンマ区切り値 (CSV) ファイルに一括ダウンロードできます。

## <a name="to-download-a-list-of-groups"></a>グループの一覧をダウンロードするには

1. 組織の管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. Azure AD で、 **[グループ]** 、 **[グループのダウンロード]** の順に選択します。
1. **[グループのダウンロード]** ページで **[開始]** を選択すると、グループの一覧を含む CSV ファイルが得られます。

   ![[グループのダウンロード] コマンドは [すべてのグループ] ページにあります。](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>ダウンロードの状態を確認する

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

グループの一覧を一括ダウンロードするごとに、最大 1 時間かかることがあります。 少なくとも 300,000 グループの一覧を一括でダウンロードできます。

## <a name="next-steps"></a>次のステップ

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
