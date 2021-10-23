---
title: セルフサービス グループ管理の設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory にセキュリティ グループまたは Microsoft 365 グループを作成して管理したり、セキュリティ グループまたは Microsoft 365 グループのメンバーシップを要求したりすることができます
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 07/27/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: faebf53c9774e71b30003903b9c472ccdcd3c493
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986133"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory でのセルフサービス グループ管理の設定 

Azure Active Directory (Azure AD) では、独自のセキュリティ グループまたは Microsoft 365 グループを作成して管理することができます。 グループの所有者は、メンバーシップ要求を承認または拒否できます。また、グループ メンバーシップの制御を委任できます。 セルフサービスによるグループ管理機能は、メールを有効にしたセキュリティ グループまたは配布リストでは使用できません。

## <a name="self-service-group-membership-defaults"></a>セルフ サービス グループ メンバーシップの既定値

Azure portal で、または Azure AD PowerShell を使用してセキュリティ グループが作成された場合、グループの所有者のみがメンバーシップを更新できます。 所有者による承認か自動承認かにかかわらず、[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/joinGroups)でセルフサービスにより作成されたセキュリティ グループおよびすべての Microsoft 365 グループにはすべてのユーザーが参加できます。 アクセス パネルでは、グループを作成するときにメンバーシップ オプションを変更できます。

グループの作成場所 | セキュリティ グループの既定の動作 | Microsoft 365 グループの既定の動作
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | メンバーを追加できるのは所有者のみです<br>アクセス パネルには表示されますが、参加できません | すべてのユーザーが参加できます
[Azure Portal](https://portal.azure.com) | メンバーを追加できるのは所有者のみです<br>アクセス パネルには表示されますが、参加できません<br>グループ 作成時に所有者は自動的に割り当てられません | すべてのユーザーが参加できます
[アクセス パネル](https://account.activedirectory.windowsazure.com/r#/joinGroups) | すべてのユーザーが参加できます<br>グループの作成時に、メンバーシップ オプションを変更できます | すべてのユーザーが参加できます<br>グループの作成時に、メンバーシップ オプションを変更できます

## <a name="self-service-group-management-scenarios"></a>セルフサービス グループ管理のシナリオ

* **グループ管理の委任** 管理者が、社内で利用している SaaS アプリケーションへのアクセスを管理しているとします。 それらのアクセス権を管理する負担が大きくなってきたことから、この管理者はビジネス オーナーに依頼して新しいグループを作成してもらいます。 管理者はアプリケーションへのアクセス権を新しいグループに割り当て、既にアプリケーションにアクセスしているすべてのユーザーをそのグループに追加します。 これ以降はビジネス オーナーがユーザーをさらに追加できます。追加されたユーザーはアプリケーションへと自動的にプロビジョニングされます。 ビジネス オーナーは、管理者によるユーザーのアクセス管理が行われるまで待つ必要がありません。 管理者が同じ権限を別のビジネス グループのマネージャーに付与した場合、その人物も自分のグループ メンバーのアクセス管理ができるようになります。 ビジネス オーナーもマネージャーも、互いのグループ メンバーを表示または管理することはできません。 管理者は依然として、そのアプリケーションへのアクセス権を持ったすべてのユーザーを表示でき、必要に応じてアクセス権をブロックすることができます。
* **グループ管理のセルフサービス化** たとえば、2 人のユーザーが別々に SharePoint Online サイトを設定しているとします。 この 2 人のユーザーは、自分のサイトへのアクセス権を互いのチームに付与しようとしています。 これを実現するには、Azure AD に 1 つのグループを作成し、SharePoint Online で両者がそのグループを選択して、それぞれのサイトへのアクセス権を付与します。 アクセス権を必要とする人がいれば、その人物がアクセス パネルからアクセス権を要求します。承認が下りればその人物は自動的に、両方の SharePoint Online サイトにアクセスできるようになります。 後日、2 人のうち一方が、サイトにアクセスしているすべてのユーザーに、特定の SaaS アプリケーションへのアクセス権も付与することに決めたとします。 SaaS アプリケーションの管理者は、アプリケーションによる SharePoint Online サイトへのアクセス権を追加することができます。 それ以後は、要求が承認されると、2 つの SharePoint Online サイトへのアクセス権に加え、この SaaS アプリケーションへのアクセス権も付与されるようになります。

## <a name="make-a-group-available-for-user-self-service"></a>グループをユーザーのセルフ サービスに使用できるようにする

1. ディレクトリの全体管理者ロールまたは特権ロール管理者のロールが割り当てられているアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[グループ]** を選択し、 **[全般]** 設定を選択します。

    ![Azure Active Directory グループの全般設定。](./media/groups-self-service-management/groups-settings-general.png)

1. **[所有者はアクセス パネルでのグループ メンバーシップの要求を管理できる]** を **[はい]** に設定します。

1. **[アクセス パネルのグループ機能にアクセスするユーザー機能を制限する]** を **[いいえ]** に設定します。

1. **[Users can create security groups in Azure portals, API or PowerShell]\(ユーザーは Azure portal、API、または PowerShell でセキュリティ グループを作成できる\)** を **[はい]** または **[いいえ]** に設定します。

    この変更の詳細については、次のセクション「[グループ設定](#group-settings)」を参照してください。

1. **[Users can create Microsoft 365 groups in Azure portals, API or PowerShell]\(ユーザーは Azure portal、API、または PowerShell で Microsoft 365 グループを作成できる\)** を **[はい]** または **[いいえ]** に設定します。

    この変更の詳細については、次のセクション「[グループ設定](#group-settings)」を参照してください。

また、**Azure portal でメンバーをグループの所有者として割り当てることができる所有者** を使用して、ユーザーのセルフサービスのグループ管理よりも詳細なアクセス制御を実現することもできます。

ユーザーがグループを作成できる場合、自分の組織内のすべてのユーザーが、新しいグループを作成でき、既定の所有者としてそれらのグループにメンバーを追加できるようになります。 独自のグループを作成できる個人を指定することはできません。 個人を指定できるのは、別のグループ メンバーをグループの所有者にする場合のみです。

> [!NOTE]
> ユーザーが セキュリティ グループまたは Microsoft 365 グループへの参加を要求したり、所有者がメンバーシップ要求を承認または拒否するように要求するには、Azure Active Directory Premium (P1 または P2) ライセンスが必要です。 Azure Active Directory Premium ライセンスがない場合でも、ユーザーはアクセス パネルでグループを管理できますが、アクセス パネルで所有者の承認を必要とするグループを作成することはできません。また、グループに参加するように要求することもできません。

## <a name="group-settings"></a>グループ設定

グループ設定を使用すると、セキュリティ グループと Microsoft 365 グループを作成できるユーザーを制御できます。

![Azure Active Directory セキュリティ グループ設定の変更。](./media/groups-self-service-management/security-groups-setting.png)

> [!NOTE]
> 最近、これらの設定の動作が変更されました。 これらの設定が組織に合わせて構成されていることを確認してください。 詳細については、「[グループ設定が変更された理由](#why-were-the-group-settings-changed)」を参照してください。

 次の表は、選択する値を決定するのに役立ちます。

| 設定 | 値 | テナントへの影響 |
| --- | :---: | --- |
| ユーザーは Azure portal、API、または PowerShell でセキュリティ グループを作成できる | はい | Azure AD 組織内のすべてのユーザーが、Azure portal、API、または PowerShell を使用して、新しいセキュリティ グループを作成し、それらのグループにメンバーを追加できます。 これらの新しいグループは、他のすべてのユーザーのアクセス パネルにも表示されるようになります。 グループのポリシー設定で許可されている場合、他のユーザーはこれらのグループへの参加要求を作成できます。 |
|  | いいえ | ユーザーは、セキュリティ グループを作成することも、所有している既存のグループを変更することもできません。 ただし、それらのグループのメンバーシップの管理と、他のユーザーからのグループへの参加要求の承認は、引き続き行うことができます。 |
| ユーザーは Azure portal、API、または PowerShell で Microsoft 365 グループを作成できる | はい | Azure AD 組織内のすべてのユーザーが、Azure portal、API、または PowerShell を使用して、新しい Microsoft 365 グループを作成し、それらのグループにメンバーを追加できます。 これらの新しいグループは、他のすべてのユーザーのアクセス パネルにも表示されるようになります。 グループのポリシー設定で許可されている場合、他のユーザーはこれらのグループへの参加要求を作成できます。 |
|  | いいえ | ユーザーは、Microsoft 365 グループを作成することも、所有している既存のグループを変更することもできません。 ただし、それらのグループのメンバーシップの管理と、他のユーザーからのグループへの参加要求の承認は、引き続き行うことができます。 |

これらのグループ設定に関する追加の詳細を示します。

- これらの設定は、有効になるまでに最大 15 分かかる場合があります。
- ユーザー全員ではなく一部のユーザーがグループを作成できるようにする場合は、グループを作成できるロール ([グループ管理者](../roles/permissions-reference.md#groups-administrator)など) をそれらのユーザーに割り当てることができます。
- これらの設定はユーザー向けであり、サービス プリンシパルには影響しません。 たとえば、グループを作成するアクセス許可を備えたサービス プリンシパルがある場合、これらの設定を **[いいえ]** に設定したとしても、サービス プリンシパルは引き続きグループを作成できます。 

### <a name="why-were-the-group-settings-changed"></a>グループ設定が変更された理由

グループ設定の以前の実装は **[ユーザーは Azure portal でセキュリティ グループを作成できる]** および **[Users can create Microsoft 365 groups in Azure portals]\(ユーザーは Azure portal で Microsoft 365 グループを作成することができる\)** でした。 以前の設定は、Azure portal でのグループの作成のみを制御し、API または PowerShell には適用されませんでした。 新しい設定は、Azure portal に加えて API と PowerShell でのグループの作成を制御します。 新しい設定はより安全です。

新しい設定の既定値は、以前の API または PowerShell の値に設定されています。 新しい設定の既定値は、Azure portal の動作のみを制御していた以前の値とは異なる可能性があります。 2021 年 5 月以降、数週間の移行期間が設けられ、新しい設定が有効になる前に希望する既定値を選択できました。 現在は新しい設定が有効になっているので、組織に対して新しい設定が構成されていることを確認する必要があります。

## <a name="next-steps"></a>次のステップ

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory のグループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)
* [Azure Active Directory のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
