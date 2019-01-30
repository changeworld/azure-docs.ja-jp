---
title: Azure Active Directory Identity Protection の FAQ | Microsoft Docs
description: Azure AD Identity Protection についてよく寄せられる質問
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 70bab69fecbe3f8aab1858aaddfa9004278c0f03
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470683"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory Identity Protection の FAQ

この記事では、Azure Active Directory (Azure AD) Identity Protection に関してよく寄せられる質問の回答を示します。 詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」をご覧ください。 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>一部のリスク イベントの状態が "クローズ (システム)" になっているのはなぜですか。

**A:** これらは、Identity Protection によって検出された後、危険性が低下したと見なされたためにクローズされたリスク イベントです。 これらのイベントは、ユーザーのリスク レベルには加算されません。 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Azure Portal で Identity Protection を使用するために、全体管理者である必要はありますか。
**A:** いいえ。 セキュリティ閲覧者、セキュリティ管理者、全体管理者のいずれかになることで、Identity Protection を使用できます。

---

## <a name="how-do-i-get-identity-protection"></a>Identity Protection を入手するには、どうすればよいですか。

**A:** この質問の回答については、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>"リスクのフラグ付きユーザー" でユーザーを並べ替えるにはどうすればよいですか。

**A:****[リスクのフラグ付きユーザー]** ページの上部にある **[ダウンロード]**  をクリックして、リスクのフラグ付きユーザー レポートをダウンロードします。 [最終更新日時 (UTC)] など、使用可能なフィールドに基づいて、ダウンロードされたデータを並べ替えることができます。

---
