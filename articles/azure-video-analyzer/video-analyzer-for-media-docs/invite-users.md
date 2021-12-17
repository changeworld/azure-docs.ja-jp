---
title: Azure Video Analyzer for Media (以前の Video Analyzer for Media) にユーザーを招待する - Azure
description: この記事では、Azure Video Analyzer for Media (以前の Video Analyzer for Media) にユーザーを招待する方法を示します。
ms.topic: quickstart
ms.date: 09/14/2021
ms.openlocfilehash: 791e9e7d3090aaa79cd754135f26e79fae3a9e0a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650773"
---
# <a name="quickstart-invite-users-to-video-analyzer-for-media"></a>クイックスタート: Video Analyzer for Media にユーザーを招待する

同僚と共同作業を行うには、その人たちを Azure Video Analyzer for Media (旧称 Video Indexer) アカウントに招待することができます。 

> [!NOTE]
> ユーザーを追加または削除できるのは、アカウントの管理者だけです。</br>
> 有料アカウントを使用する場合、Azure AD ユーザーのみを招待できます。

## <a name="invite-new-users"></a>新しいユーザーを招待する

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトにサインインします。 管理者アカウントを使用して接続していることを確認します。
1. 管理者には、右上隅に **[Share account]\(アカウントの共有\)** ボタンが表示されます。 このボタンをクリックして、ユーザーを招待することができます。 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="アカウントを共有する":::
1. **[Share this account with others]\(このアカウントを他のユーザーと共有する\)** ダイアログで、ご利用の Video Analyzer for Media アカウントに招待したいユーザーのメール アドレスを入力します。

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="このアカウントにユーザーを招待する":::  
1. **[Invite]\(招待\)** を押すと、保留中の招待状のリストにそのユーザーが追加されます。 <br/>アカウントにまだ参加していない各招待者については、**招待を削除** するオプションと **招待 URL をコピーする** オプションのどちらかを選択できます。

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="保留中の招待状":::  
1. 招待された人がアカウントに参加すると、3 つのオプションを選択できるようになります。 そのうち 2 つはロールに関するオプション、つまり、 **[共同作成者]** (既定) と **[所有者]** です。 または、 **[削除]** をクリックして招待者を削除することもできます。

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="参加している招待者":::  

    ユーザーは、削除されても通知を受け取りません。 削除されると、ユーザーはログインを承認されません。

## <a name="manage-roles-invite-more-users"></a>ロールの管理とその他のユーザーの招待

**[Share this account with others]\(このアカウントを他のユーザーと共有する\)** ダイアログは、前述のように **[Share account]\(アカウントの共有\)** をクリックして表示できるほか、 **[設定]** から表示することもできます。

1. 開いているアカウントの **[設定]** ボタンをクリックします。 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="アカウント設定":::  
1. **[ロールの管理]** ボタンをクリックします。
1. 他のユーザーを招待するには、 **[Invite more people to this account]\(このアカウントに他のユーザーを招待する\)** をクリックします。

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="[その他のユーザーの招待]":::  
1. **[invite more people to this account]\(このアカウントに他のユーザーを招待する\)** をクリックすると、招待ダイアログが表示されます。
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="このアカウントにユーザーを招待する":::  

## <a name="next-steps"></a>次のステップ

[Video Analyzer for Media Web サイト](video-indexer-view-edit.md)または [Video Analyzer for Media 開発者ポータル](video-indexer-use-apis.md)を使用して、ビデオの分析情報を確認できるようになりました。

## <a name="see-also"></a>関連項目

- [Video Analyzer for Media の概要](video-indexer-overview.md)
- [サインアップして最初のビデオをアップロードする方法](video-indexer-get-started.md)
- [API の使用開始](video-indexer-use-apis.md)
