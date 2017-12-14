---
title: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを Azure AD で作成する | Microsoft Docs"
description: "アプリケーションにアクセスするユーザーまたはグループのメンバーに対するアクセス レビューを作成する方法について説明します。"
services: active-directory
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
ms.openlocfilehash: b2f8985f12e17ac69543cfb3a33725f796eedde8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>グループ メンバーまたはアプリケーション アクセスに対するアクセス レビューを Azure AD で作成する

アクセス権の割り当ては、ユーザーに割り当てられているアクセス権が不要になった時点で "古く" なっています。 アクセス権の割り当てが古いことで生じるリスクを軽減するために、管理者は、アプリケーションに割り当てられているユーザーまたはグループのメンバーに対するアクセス レビューを Azure Active Directory (Azure AD) を使用して作成することができます。 これらのシナリオの詳細については、[ユーザー アクセスの管理](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)と[ゲスト アクセスの管理](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)に関するページを参照してください。 

## <a name="create-an-access-review"></a>アクセス レビューの作成

1. グローバル管理者として[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動し、**[プログラム]** を選択します。

2. 作成するアクセス レビュー コントロールの従属先となるプログラムを選択します。 "**既定のプログラム**" が最初から存在するほか、別のプログラムを作成することもできます。 たとえば、コンプライアンスの取り組みやビジネス目標ごとにプログラムを 1 つ用意することができます。

3. そのプログラム内の **[コントロール]** を選択し、**[追加]** を選択してコントロールを追加します。

4. それぞれのアクセス レビューに名前を付けます。 必要に応じて、各レビューの説明を入力してください。 この名前はレビュー担当者に表示されます。

5. 開始日と終了日を設定します。 既定では、アクセス レビューが、その作成日に開始され、1 か月後に終了します。 この開始日と終了日を変更することで、アクセス レビューを後で開始し、必要な日数を好きなだけ確保することができます。

6. アクセス レビューは、アプリケーションに割り当てられているグループのメンバーまたはユーザーを対象とすることができます。 また、アクセス レビューの範囲は限定することもでき、アプリケーションのメンバーになっているユーザー、つまりアプリケーションへのアクセス権を持つユーザー全員をレビューすることはせずに、メンバーとなっている (アプリケーションに割り当てられている) ゲスト ユーザーだけをレビュー対象にすることができます。

7. 対象範囲内の全ユーザーをレビューする担当者 (複数可) を選ぶか、 メンバー自身にそのアクセス権をレビューしてもらうことができます。 リソースがグループである場合は、そのグループの所有者にレビューを依頼することができます。 アクセスの承認時に理由を入力するようレビュー担当者に要求することもできます。

8. 最後に、**[開始]** を選択します。


## <a name="manage-the-access-review"></a>アクセスレビューを管理する

既定では、レビューの開始直後に Azure AD からレビュー担当者宛てにメールが送信されます。 Azure AD からメールを送信することを選択しなかった場合は、アクセス レビューが実行待ちになっていることを必ずレビュー担当者に伝えてください。 レビュー担当者には、[アクセスをレビュー](active-directory-azure-ad-controls-perform-access-review.md)する手順を案内することができます。 レビュー対象がゲストで、その本人にアクセス レビューを実施してもらう場合は、[自身のアクセス権をレビューする手順](active-directory-azure-ad-controls-perform-access-review.md)を案内してください。

レビュー担当者の中にゲストが含まれる場合、メールで通知されるのは、あらかじめ招待状を受け取ったゲストだけです。


レビュー担当者が Azure AD ダッシュボードでレビューを完了すると、**[アクセス レビュー]** セクションで進行状況を追跡できます。 ディレクトリのアクセス権は、[レビューが完了する](active-directory-azure-ad-controls-complete-access-review.md)まで変更されません。

## <a name="next-steps"></a>次のステップ

アクセス レビューが開始されると、アクセス権をレビューするよう促すメールが Azure AD からレビュー担当者に自動的に送信されます。 メールが届かなかったユーザーには、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページの手順を送信できます。 

アクセス レビュー期間が過ぎるか、管理者がアクセス レビューを停止したら、[アクセス レビューの実施](active-directory-azure-ad-controls-complete-access-review.md)に関するページの手順に従って、その結果を確認したうえで適用します。


