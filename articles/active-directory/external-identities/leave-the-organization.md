---
title: ゲスト ユーザーとして組織を脱退する - Azure Active Directory
description: Azure AD B2B のゲスト ユーザーがアクセス パネルから組織を脱退する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547c27b8faf9f282c836bdda2d57bf9d13603423
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780701"
---
# <a name="leave-an-organization-as-a-guest-user"></a>ゲスト ユーザーとして組織を脱退する

組織からアプリを利用する必要がなくなるか、関連付けを維持する必要がなくなった場合、Azure Active Directory (Azure AD) B2B のゲスト ユーザーはいつでも組織を脱退する決定をすることができます。 ユーザーは、管理者に連絡することなく独自の判断で組織を脱退することができます。

> [!NOTE]
> ホーム テナントまたはリソース テナントのいずれかでアカウントが無効な場合、ゲスト ユーザーは組織を離れることができません。 アカウントが無効な場合は、ゲスト ユーザーがテナント管理者に連絡する必要があります。テナント管理者は、ゲスト アカウントを削除するか、ゲスト アカウントを有効にしてユーザーが組織を離れられるようにします。

## <a name="leave-an-organization"></a>組織を脱退する

組織を脱退するには、以下の手順を実行します。

1. 次のいずれかを実行して、 **[マイ アカウント]** ページに移動します。
- 職場または学校アカウントを使用している場合は、 https://myaccount.microsoft.com にアクセスしてサインインします。
- 個人アカウントを使用している場合は、 https://myapps.microsoft.com にアクセスしてサインインし、右上にあるアカウント アイコンをクリックして、 **[アカウントの表示]** を選択します。 または、テナント情報を含むマイ アカウントの URL を使用して、[マイ アカウント] ページに直接アクセスします (例については、以下の注意を参照してください)。  
   > [!NOTE]
   > サインイン時に電子メールのワンタイム パスコード機能を使用する場合は、テナント名またはテナント ID を含むマイ アカウントの URL (`https://myaccount.microsoft.com?tenantId=wingtiptoys.onmicrosoft.com` や `https://myaccount.microsoft.com?tenantId=ab123456-cd12-ef12-gh12-ijk123456789`など) を使用する必要があります。

2. **Organizations** のところで脱退する組織を確認し、**Leave organization** を選択します。

   ![ユーザー インタフェースにある組織を脱退するオプションのスクリーンショット](media/leave-the-organization/leave-org.png)
3. 確認を求められたら、**Leave** を選択します。

> [!NOTE]
   > ホーム組織から脱退することはできません。

## <a name="account-removal"></a>アカウントの削除

組織を脱退すると、そのユーザーのアカウントはディレクトリから "論理的に削除" されます。 既定では、ユーザーのオブジェクトは Azure AD の **[削除済みのユーザー]** エリアに移動しますが、30日の間は完全には削除されません。 この論理的削除により、ユーザーが 30 日の期間内にアカウントの復活を要求した場合、管理者はそのユーザー アカウント （グループおよびアクセス許可を含む） を復活させることができます。

30 日の期間中、テナント管理者はいつでも必要に応じてアカウントを完全削除できます。 これを行うには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[管理]** にある **[ユーザー]** を選択します。
3. **Deleted users** を選択します。
4. 削除済みユーザーの横のチェック ボックスを選択して、**Delete permanently** を選択します。

ユーザーを完全削除した場合、操作の取り消しはできません。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次のステップ

- Azure AD B2B の概要については、「[Azure AD B2B コラボレーションとは](what-is-b2b.md)」を参照してください。



