---
title: アクセス権をレビューする | Microsoft Docs
description: Azure Active Directory でアクセス権をレビューする
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
ms.openlocfilehash: f85f83ff103818b826ca2c61c41fbbef67e98c14
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622230"
---
# <a name="review-your-access"></a>アクセス権をレビューする

Azure Active Directory (Azure AD) には、Azure AD をはじめとする Microsoft Online Services 内のグループのメンバー管理やアプリケーションへのアクセス管理を省力化する "アクセス レビュー" という機能が備わっています。 皆さんには、アプリケーションへのアクセス権が付与されたユーザーまたはグループのメンバーに関して、そのアクセス権をレビューするよう求めるメールが Microsoft から送信される場合があります。 

## <a name="open-an-access-review"></a>アクセス レビューを開始する

実行待ちのアクセス レビューを表示するには、メールに記載されているレビュー アクセス リンクをクリックします。 2018 年 8 月より、Azure AD ロール向けの電子メール通知のデザインが変わります。 ユーザーがレビューアーになるように招待されるときに送信される電子メールの例を次に示します。

![アクセスのレビューの電子メール](./media/active-directory-azure-ad-controls-how-to-review-your-access/new-ar-email.png)

メールが届かない場合は、次の手順でアクセス レビューの場所を確認できます。

1. [Azure AD アクセス パネル](https://myapps.microsoft.com)にサインインします。

2. このページの右上隅にあるユーザー記号を選択すると、自分の名前と既定の組織が表示されます。 複数の組織が一覧表示されている場合は、アクセス レビューの依頼元の組織を選択してください。

3. ページの右側に**アクセス レビュー**という名称のタイルがある場合は、それを選択します。 このタイルが表示されない場合は、その組織に対して実施するアクセス レビューがないので、現時点で必要な対応はありません。

## <a name="fill-out-an-access-review"></a>アクセス レビューに回答する

一覧からアクセス レビューを選択すると、自分のアクセス権が表示されます。 目的の行を選択して、アクセス権の継続を承認するか拒否するかを選んでください。

アクセス権の継続を承認することについて、その正当性を説明するよう求められる場合もあります。

## <a name="next-steps"></a>次の手順

拒否されたアクセス権はすぐには削除されません。 回答と承認の内容に変更がある場合は、回答をいったんリセットしてから改めて選択してください。 この手順は、アクセス レビューが完了するまで行うことができます。






