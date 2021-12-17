---
title: アクセス レビューでリソースに対する自分のアクセスをレビューする - Azure AD
description: リソースに対する自分のアクセスを Azure Active Directory アクセス レビューでレビューする方法を学習します。
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/27/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a997e4f92e1336d75f1c9c690b4c84ae0bddcf6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524153"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理におけるアクセス パッケージとリソースの自己レビュー

Azure Active Directory (Azure AD) エンタイトルメント管理を使うと、企業がグループ、アプリケーション、SharePoint サイトへのアクセスを管理する方法が簡単になります。 この記事では、割り当てられたアクセス パッケージの自己レビューを行う方法について説明します。

## <a name="review-your-own-access-by-using-my-access"></a>マイ アクセスを使用して自分のアクセス権をレビューする

グループ、アプリケーション、またはアクセス パッケージへのご自分のアクセス権を確認する方法が 2 つあります。

### <a name="use-email"></a>メールを使用する

>[!IMPORTANT]
> メールの受信に遅延が生じることがあり、場合によっては最大 24 時間かかることがあります。 安全な受信者の一覧に azure-noreply@microsoft.com を追加して、すべてのメールを確実に受信できるようにしてください。

1. アクセス レビューを実行するように求める Microsoft からのメールを見つけます。 メールのメッセージの例を次に示します。

   ![グループに対するアクセスの確認を求める Microsoft からのメールの例を示すスクリーンショット。](./media/self-access-review/access-review-email-preview.png)

1. **[アクセスのレビュー]** を選択してアクセス レビューを開きます。

1. 次に「**アクセス レビューを実行する**」セクションに移ります。

### <a name="use-my-access"></a>マイ アクセスを使う

お使いのブラウザーを使用して **[マイ アクセス]** を開くと、保留中のご自分のアクセス レビューを表示することもできます。

1. [[マイ アクセス]](https://myaccess.microsoft.com/) にサインインします。

1. 左側のメニューから **[アクセス レビュー]** を選択すると、ご自分に割り当てられている保留中のアクセス レビューの一覧が表示されます。

   ![メニューの [アクセス レビュー] を示すスクリーンショット。](./media/self-access-review/access-review-menu.png)

## <a name="do-the-access-review"></a>アクセス レビューを行う

1. **[グループとアプリ]** の下に次が表示されます。

    - **名前**: アクセス レビューの名前。
    - **期限**: レビューの期限。 この日付を過ぎると、拒否されたユーザーはレビュー対象のグループまたはアプリから削除される場合があります。
    - **リソース**: レビュー中のリソースの名前。
    - **進行状況**: このアクセス レビューに関わるユーザーの合計数に対する、レビューされたユーザーの数。

1. 開始するには、アクセス レビューの名前を選びます。

   ![アプリとグループの保留中のアクセス レビューの一覧を示すスクリーンショット。](./media/self-access-review/access-reviews-list-preview.png)

1. ご自分のアクセスをレビューし、アクセスが引き続き必要かどうかを判断します。

    他のユーザーのアクセスをレビューするように要求した場合は、ページの外観が異なります。 詳細については、[グループまたはアプリケーションに対するアクセスのレビュー](perform-access-review.md)に関するページを参照してください。

    ![開いているアクセス レビューのスクリーンショット。グループへのアクセスが引き続き必要かどうかを確認しています。](./media/self-access-review/review-access-preview.png)

1. **[はい]** を選択してご自分のアクセスを維持するか、 **[いいえ]** を選択してご自分のアクセスを削除します。

1. **[はい]** を選択する場合は、**[理由]** ボックスに正当性を指定する必要がある場合があります。

    ![[はい] を選択してグループへのアクセスを維持する方法を示すスクリーンショット。](./media/self-access-review/review-access-yes-preview.png)

1. **[送信]** を選びます。

    選択内容が送信され、 **[マイ アクセス]** ページに戻ります。

    応答内容を変更する場合は、 **[アクセス レビュー]** ページをもう一度開き、応答内容を更新します。 応答内容は、アクセス レビューが終了するまでいつでも変更できます。

    > [!NOTE]
    > アクセスが不要になったことを指定した場合、すぐには削除されません。 レビューが終了したか、管理者によりレビューが停止されたときに削除されます。

## <a name="next-steps"></a>次のステップ

[グループまたはアプリケーションのアクセス レビューを完了する](complete-access-review.md)
