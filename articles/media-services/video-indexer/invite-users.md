---
title: Video Indexer にユーザーを招待する - Azure
titleSuffix: Azure Media Services
description: この記事では、Video Indexer にユーザーを招待する方法について説明します。
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 4023f8584e7b901a19906235d98982abbbfbaaf8
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364144"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>クイック スタート:Video Indexer にユーザーを招待する

同僚と共同作業を行うために、自分の Video Indexer アカウントに招待することができます。 

> [!NOTE]
> ユーザーを追加または削除できるのは、アカウントの管理者だけです。

## <a name="invite-new-users"></a>新しいユーザーを招待する

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトにサインインします。 管理者アカウントを使用して接続していることを確認します。
1. 管理者には、右上隅に **[Share account]\(アカウントの共有\)** ボタンが表示されます。 このボタンをクリックして、ユーザーを招待することができます。 

   ![新しいユーザーを招待する](./media/invite-users/share-account.png)
1. Video Indexer アカウントに追加するユーザーの電子メールアドレスを追加します。

    ![このアカウントにユーザーを招待する](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > 招待するすべてのユーザーには、アカウント内のすべてのビデオに対する読み取りアクセス許可と書き込みアクセス許可が付与されます。
1. 招待したユーザーには、リンクを含む電子メールが送信されます。 **[Join Video Indexer]\(Video Indexer に参加する\)** リンクをクリックすると、アカウントにアクセスできるようになります。

    ![確認](./media/invite-users/invite-msg.png)

    ユーザーは、アカウントにアクセスできるようになるために、リンクをクリックして参加する必要があります。 

## <a name="removing-existing-users"></a>既存のユーザーを削除する

アカウントにアクセスできるユーザーを削除する場合は、名前の横にある **[X]** 記号をクリックします。

![Remove users](./media/invite-users/remove-users.png)

ユーザーは、削除されても通知を受け取りません。 削除されると、ユーザーはログインを承認されません。

## <a name="next-steps"></a>次のステップ

これで、[Video Indexer Web サイト](video-indexer-view-edit.md)または [Video Indexer 開発者ポータル](video-indexer-use-apis.md)を使用してビデオの分析情報を表示できます。

## <a name="see-also"></a>関連項目

- [Video Indexer の概要](video-indexer-overview.md)
- [サインアップして最初のビデオをアップロードする方法](video-indexer-get-started.md)
- [API の使用開始](video-indexer-use-apis.md)
