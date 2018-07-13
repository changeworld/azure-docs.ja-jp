---
title: ゲスト ユーザーとして組織を脱退する - Azure Active Directory | Microsoft Docs
description: Azure AD B2B のゲスト ユーザーがアクセス パネルから組織を脱退する方法について説明します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 17b34b173a10a355817fee0f5928b7fb478125e3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38506522"
---
# <a name="leave-an-organization-as-a-guest-user"></a>ゲスト ユーザーとして組織を脱退する

組織からアプリを利用する必要がなくなるか、関連付けを維持する必要がなくなった場合、Azure Active Directory (Azure AD) B2B のゲスト ユーザーはいつでも組織を脱退する決定をすることができます。 ユーザーは、管理者に連絡することなく独自の判断で組織を脱退することができます。

## <a name="leave-an-organization"></a>組織を脱退する

組織を脱退するには、[アクセス パネル](https://myapps.microsoft.com)にサインインしている状態で、次の操作をします。

1. 脱退する組織にサインインしていない場合は、右上隅から自分の名前を選択し、脱退する組織をクリックします。
2. 右上隅から自分の名前を選択します。
3. **Organizations** の横の ［設定］ (歯車) アイコンを選択します。
 
   ![アクセス パネルのユーザー設定のスクリーンショット](media/leave-the-organization/UserSettings.png) 

3. **Organizations** のところで脱退する組織を確認し、**Leave organization** を選択します。

   ![ユーザー インタフェースにある組織を脱退するオプションのスクリーンショット](media/leave-the-organization/LeaveOrg.png)

4. 確認を求められたら、**Leave** を選択します。 

## <a name="account-removal"></a>アカウントの削除

組織を脱退すると、そのユーザーのアカウントはディレクトリから "論理的に削除" されます。 既定では、ユーザーのオブジェクトは Azure AD の **Deleted users** エリアに移動しますが、30日の間は完全には削除されません。 この論理的削除により、ユーザーが 30 日の期間内にアカウントの復活を要求した場合、管理者はそのユーザー アカウント （グループおよびアクセス許可を含む） を復活させることができます。

30 日の期間中、テナント管理者はいつでも必要に応じてアカウントを完全削除できます。 これを行うには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]** を選択します。
2. **[管理]** にある **[Users]** を選択します。
3. **Deleted users** を選択します。
4. 削除済みユーザーの横のチェック ボックスを選択して、**Delete permanently** を選択します。

ユーザーを完全削除した場合、操作の取り消しはできません。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次の手順

- Azure AD B2B の概要については、「[Azure AD B2B コラボレーションとは](what-is-b2b.md)」を参照してください。



