---
title: ユーザー割り当ての要求 - Azure AD | Microsoft Docs
description: Azure アプリケーションにユーザーの割り当てを要求する方法です。
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 30b78cba-1e0f-472f-8314-f2250a9b91c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: kgremban
robots: noindex
ms.openlocfilehash: 4519681d9b91383d27c00a992f85b0cb5d74f235
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859464"
---
# <a name="azure-ad-and-applications-require-user-assignment"></a>Azure AD とアプリケーション: ユーザー割り当ての要求
## <a name="requiring-user-assignment"></a>ユーザー割り当ての要求
1. 管理者アカウントを使用して、Azure Portal にログインします。
2. メイン メニューの **[すべてのサービス]** をクリックします。
3. アプリケーションに使用するディレクトリを選択します。
4. **[エンタープライズ アプリケーション]** タブをクリックします。
5. このディレクトリに関連付けられているアプリケーションの一覧から、アプリケーションを選択します。
6. **[プロパティ]** タブをクリックします。
7. **[ユーザーの割り当てが必要ですか?]** を [はい] に変更します。
8. 画面上部にある **[保存]** ボタンをクリックします。

ここで、アプリケーションにユーザーやグループを割り当てる必要があります。 「[アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md)」と「[アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
