---
title: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure AD で完了する | Microsoft Docs"
description: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure Active Directory で完了する方法について説明します。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: de853d633aa65c9f08f5e28088d5240c2e4d7fa6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure AD で完了する

管理者は、アプリケーションに割り当てられているユーザーまたはグループのメンバーに対する[アクセス レビューを Azure Active Directory (Azure AD) を使用して作成](active-directory-azure-ad-controls-create-access-review.md)することができます。 レビュー担当者には、アクセス権をレビューするよう促すメールが Azure AD から自動的に送信されます。 メールが届かなかったユーザーには、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページの手順を送信できます。 アクセス レビュー期間が過ぎるか、管理者がアクセス レビューを停止したら、この記事の手順に従って、その結果を確認したうえで適用します。

## <a name="view-an-access-review-in-the-azure-portal"></a>Azure Portal でのアクセス レビューの確認

1. [アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動して **[プログラム]** を選択し、アクセス レビュー コントロールが含まれたプログラムを選択します。

2. **[管理]** を選択してアクセス レビュー コントロールを選択します。 選択したプログラムにコントロールが多数存在する場合は、特定の種類のコントロールだけをフィルターで抽出したり、コントロールの状態で並べ替えたりすることができます。 アクセス レビュー コントロールの名前またはそれを作成した所有者の表示名で検索することも可能です。 

## <a name="stop-a-review-that-hasnt-finished"></a>未完了のレビューの停止

レビューが終了予定日に達していない場合に、管理者は **[停止]** を選択することで、予定よりも早くレビューを終了することができます。 停止後は、ユーザーをレビューできなくなります。 レビューを停止後に再開することはできません。

## <a name="apply-the-changes"></a>変更の適用 

アクセス レビューが終了日に達するか管理者により手動で停止されて完了した後、**[適用]** をクリックします。 レビューの結果が適用されて、グループまたはアプリケーションが更新されます。 レビューでアクセス権が拒否されたユーザーについては、管理者がこのオプションを選択すると、そのメンバーシップまたはアプリケーションへの割り当てが Azure AD によって削除されます。 

オンプレミス ディレクトリに由来するグループ (動的グループ) は、**[適用]** を選択しても影響を受けません。 オンプレミス ディレクトリに由来したグループに変更を加える必要がある場合は、結果をダウンロードし、そのディレクトリ内の対応するグループに対して必要な変更を適用してください。

## <a name="download-the-results-of-the-review"></a>レビューの結果のダウンロード

レビューの結果を取得するには、**[承認]** を選択した後で **[ダウンロード]** を選択します。 結果の CSV ファイルは、Excel など、CSV ファイルを開くことができるプログラムで閲覧できます。

## <a name="optional-delete-a-review"></a>(省略可) レビューの削除
そのレビューが今後必要なければ、削除してかまいません。 **[削除]** を選択すると、Azure AD からレビューが削除されます。

> [!IMPORTANT]
> 削除する前に警告は表示されないため、そのレビューを削除してよいことを確認してください。
> 
> 

## <a name="next-steps"></a>次のステップ

- [Azure AD のアクセス レビューでユーザー アクセスを管理する](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Azure AD のアクセス レビューでゲスト アクセスを管理する](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Azure AD アクセス レビューに使用するプログラムとコントロールを管理する](active-directory-azure-ad-controls-manage-programs-controls.md)
- [グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](active-directory-privileged-identity-management-how-to-start-security-review.md)
