---

title: "Azure Active Directory のグループベースのライセンスとは | Microsoft Docs"
description: "Azure Active Directory のグループベース ライセンスと、その仕組み、使用方法、およびベスト プラクティスに関する説明"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 6a9cebafd1ad8f513bfab897970241f7b82b2a53
ms.openlocfilehash: 1f4e0904c9025ce0b21d904acc7b959e823039ef
ms.lasthandoff: 02/22/2017


---

# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Azure Active Directory のグループベースのライセンスとは

Microsoftの Office 365、Enterprise Mobility + Security、Dynamics CRM などのクラウド サービスやその他の類似製品では、こうしたサービスへのアクセスが必要なユーザーごとにライセンスを割り当てる必要があります。 ライセンス管理は、管理者が管理ポータル (Office、Azure) と PowerShell コマンドレットのどちらかを使用して行うことができます。 ライセンスの割り当て状態は、すべての Microsoft クラウド サービスの ID 管理に対応した基盤インフラストラクチャである Azure Active Directory に保存されます。

従来、ライセンスは個々のユーザー レベルでしか割り当てることができず、お客様が大規模な管理を行いづらい場合がありました。 たとえば、組織や部署でのユーザーの異動などの組織の変化に応じてユーザー ライセンスの追加または削除を行うには、管理者は多くの場合、クラウド サービスを一つ一つ呼び出す複雑な PowerShell スクリプトを記述する必要がありました。

こうした問題を解決するために、Azure AD ライセンス管理機能にグループベース ライセンスという新機能が導入されました。 この機能により、1 つのグループに&1; つ以上の製品ライセンスを割り当てることが可能になりました。 グループに含まれるメンバー全員に、Azure AD からライセンスが割り当てられるようになります。 グループに新しく参加したユーザーには適切なライセンスが割り当てられ、グループからユーザーが抜ける場合には割り当て済みのライセンスは削除されます。 これにより、組織や部門の構造の変化を反映するように PowerShell でユーザーごとのライセンス管理を自動化する必要はなくなりました。

## <a name="features"></a>Features (機能)

グループベース ライセンスの主な機能は次のとおりです。

- ライセンスを、Azure AD 内の任意のセキュリティ グループに割り当てられます。 セキュリティ グループは、Azure AD Connect を使用してオンプレミスから同期するか、Azure AD 内で直接作成するか (クラウド専用グループとも呼ばれます)、または Azure AD の動的グループ機能で自動作成することができます。

- 製品ライセンスをグループに割り当てる場合、管理者はその製品の&1; つまたは複数のサービス プランを無効にできます。 一般にこうした無効化は、組織で製品に含まれるサービスを使用する準備ができていない場合に行います。たとえば、管理者は Office 365 E3 製品をある部門に割り当てながら、Yammer Enterprise サービスを一時的に無効化することができます。

- ユーザーレベルのライセンスが必要なすべての Microsoft クラウド サービスがサポートされています。 サポート対象は、全 Office 365 製品、Enterprise Mobility + Security、Dynamics CRM などです。

- グループベースのライセンスは、現時点では [Azure Portal](https://portal.azure.com) を介してのみ使用できます。 他の管理ポータル (Office 365 ポータルなど) を主に使用してユーザーとグループの管理を行っているお客様は、引き続きそうしたポータルを使用しても構いませんが、グループ レベルでライセンスを管理するには Azure Portal を使用する必要があります。

- グループのメンバー変更に伴うライセンスの変更は、Azure AD により自動で管理されます。 通常、ユーザーがグループに参加またはグループから抜けた場合、そのライセンスは所属変更から数分以内に変更されます。

- ユーザーは、ライセンス ポリシーの指定により複数のグループのメンバーになることができます。また、グループ外で直接割り当てられたライセンスを持つこともできます。 ユーザーの状態は、割り当て済みのすべての製品ライセンスとサービス ライセンスの組み合わせたものになります。

- テナント内に利用できるライセンスが不足している場合や、競合するサービスが同時に割り当てられている場合などには、ライセンスをユーザーに割り当てることができません。 管理者は、グループ ライセンスを Azure AD で完全には処理できないユーザーに関する情報へアクセスし、こうした情報に基づいて是正措置を取ることができます。

- パブリック プレビュー中にグループベース ライセンス管理を使用するには、テナントに Azure AD の Basic 以上の有料サブスクリプションまたは試用版サブスクリプションが必要になります。 また、グループからライセンスを継承するユーザー全員に、有料の Azure AD エディション ライセンスが割り当てられている必要があります。

## <a name="next-steps"></a>次のステップ

グループベースのライセンスを使用したライセンス管理の他のシナリオについては、以下を参照してください。

* [Azure Active Directory でのグループへのライセンス割り当て](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)

