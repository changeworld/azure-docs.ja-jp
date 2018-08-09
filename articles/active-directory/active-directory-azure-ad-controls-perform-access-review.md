---
title: Azure AD のアクセス レビューを使ってアクセス権をレビューする | Microsoft Docs
description: Azure Active Directory のアクセス レビューを使ってアクセス権をレビューする方法について説明します。
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 07/16/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: f9ab4f5ad863fa5460b5a7ad68f00f154a16f8f0
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617889"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューでアクセス権をレビューする

Azure Active Directory (Azure AD) には、Azure AD をはじめとする Microsoft Online Services 内のグループのメンバー管理やアプリケーションへのアクセス管理を省力化する "アクセス レビュー" という機能が備わっています。 皆さんには、アプリケーションへのアクセス権が付与されたユーザーまたはグループのメンバーに関して、そのアクセス権をレビューするよう求めるメールが Microsoft から送信される場合があります。 

## <a name="open-an-access-review"></a>アクセス レビューを開始する

実行待ちのアクセス レビューを表示するには、メールに記載されているレビュー アクセス リンクをクリックします。 2018 年 8 月より、Azure AD ロール向けの電子メール通知のデザインが変わります。 ユーザーがレビューアーになるように招待されるときに送信される電子メールの例を次に示します。 

![アクセスのレビューの電子メール](./media/active-directory-azure-ad-controls-perform-access-review/new-ar-email.png)

メールが届かない場合は、次の手順でアクセス レビューの場所を確認できます。

1. [Azure AD アクセス パネル](https://myapps.microsoft.com)にサインインします。

2. このページの右上隅にあるユーザー記号を選択すると、自分の名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。

3. ページの右側に**アクセス レビュー**という名称のタイルがある場合は、それを選択します。 このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

## <a name="fill-out-an-access-review"></a>アクセス レビューに回答する

一覧からアクセス レビューを選択すると、確認が必要なユーザーの名前が表示されます。 自分のアクセス権をレビューするように求められている場合、表示される名前が 1 つ (自分の名前) だけということも考えられます。

一覧の行ごとに、ユーザーのアクセス権を承認または拒否するかどうかを指定できます。 目的の行を選択して、承認するか拒否するかを選んでください  (ユーザーがわからない場合もその旨を回答することができます)。

アクセス権の継続またはグループ メンバーシップの継続を承認することについて、その正当性を説明するよう求められる場合があります。

## <a name="next-steps"></a>次の手順

ユーザーによって拒否されたアクセス権はすぐには削除されません。 削除できる状態になるのは、レビューが完了するか、管理者がレビューを停止したときです。 回答に変更が生じて、一度拒否したユーザーを承認する場合や一度承認したユーザーを拒否する場合は、その行をクリックして回答をリセットしてから改めて回答を選択してください。 この手順は、アクセス レビューが完了するまで行うことができます。



