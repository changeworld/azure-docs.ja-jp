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
ms.openlocfilehash: ff7c9d0e24eefb1c615f45f0aa33f5a1fe0af760
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092097"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理におけるアクセス パッケージとリソースの自己レビュー

企業が Azure AD エンタイトルメント管理を使うと、グループ、アプリケーション、および SharePoint サイトへのアクセスを管理する方法が簡単になります。 この記事では、割り当てられたアクセス パッケージのセルフレビューを行う方法について説明します。

## <a name="review-your-own-access-using-my-access"></a>マイ アクセスを使用して自分のアクセス権をレビューする

次に詳細を示す 2 つの方法で、グループ、アプリケーション、またはアクセス パッケージへの自分のアクセス権を確認できます。

### <a name="email"></a>Email

>[!IMPORTANT]
> 電子メールの受信に遅延が生じる可能性があり、場合によっては最大 24 時間かかることがあります。 すべてのメールを確実に受信するため、azure-noreply@microsoft.com を信頼できる宛先のリストに追加してください。

1. アクセス レビューを実行するように求める Microsoft からのメールを見つけます。 電子メール メッセージの例は次のとおりです。

 ![グループに対するアクセスをレビューするための Microsoft からのメールの例](./media/self-access-review/access-review-email-preview.png)

2. **[アクセスのレビュー]** リンクをクリックして、アクセス レビューを開きます。

3. 次は「**アクセス レビューを実行する**」のセクションに移ります。

### <a name="directly-at-my-access"></a>マイ アクセスから直接

お使いのブラウザーを使用してマイ アクセスを開くと、保留中のご自分のアクセス レビューを表示することもできます。

1. https://myaccess.microsoft.com/ からマイ アクセスにサインインします。

2. 左側のバーのメニューから **[アクセス レビュー]** を選択すると、割り当てられている保留中のアクセス レビューの一覧が表示されます。

   ![メニューの [アクセス レビュー]](./media/self-access-review/access-review-menu.png)

## <a name="perform-the-access-review"></a>アクセス レビューを実行する

1. [グループとアプリ] の下に次が表示されます。
    
    - **[名前]** : アクセス レビューの名前。
    - **[期限]** : レビューの期限。 この日付を過ぎると、拒否されたユーザーはレビュー対象のグループまたはアプリから削除される可能性があります。
    - **[リソース]** : レビュー中のリソースの名前。
    - **[進行状況]** : このアクセス レビューに関わるユーザーの合計数に対する、レビューされたユーザー数。
    
2. 開始するには、アクセス レビューの名前をクリックします。

   ![アプリとグループについての保留中のアクセス レビューの一覧](./media/self-access-review/access-reviews-list-preview.png)

3. 自分のアクセスをレビューし、アクセスが引き続き必要かどうかを判断します。

    他のユーザーのアクセスをレビューするように要求した場合は、ページの外観が異なります。 詳細については、[グループまたはアプリケーションに対するアクセスのレビュー](perform-access-review.md)に関するページを参照してください。

    ![グループへのアクセスが引き続き必要かどうかを確認する、開かれたアクセス レビュー](./media/self-access-review/review-access-preview.png)

1. **[はい]** を選択してご自分のアクセスを維持するか、 **[いいえ]** を選択してご自分のアクセスを削除します。

1. **[はい]** をクリックする場合は、 **[理由]** ボックスに正当性を指定する必要がある場合があります。

    ![グループへのアクセスが引き続き必要かどうかを確認する、完了したアクセス レビュー](./media/self-access-review/review-access-yes-preview.png)

1. **[送信]** をクリックします。

    ご自分の選択内容が送信され、マイ アクセス ページに戻ります。

    ご自分の応答内容を変更する場合は、アクセス レビュー ページをもう一度開き、ご自分の応答内容を更新します。 応答内容は、アクセス レビューが終了するまでいつでも変更できます。

    > [!NOTE]
    > アクセスが不要になったことを指定した場合、すぐには削除されません。 レビューが終了されたか、管理者がレビューを停止したときに削除されます。

## <a name="next-steps"></a>次のステップ

- [グループまたはアプリケーションのアクセス レビューを完了する](complete-access-review.md)


