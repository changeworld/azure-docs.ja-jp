---
title: Azure AD の既定のユーザー アクセス許可を比較する | Microsoft Docs
description: メンバー、ゲスト、アプリ所有者、グループ所有者のアクセス許可を比較します。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: overview
ms.date: 01/29/2018
ms.author: lizross
ms.reviewer: vincesm
ms.openlocfilehash: e2571a6854d2c3de2425547b69c5435286182ef7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448154"
---
# <a name="default-user-permissions-in-azure-active-directory"></a>Azure Active Directory の既定のユーザー アクセス許可

Azure Active Directory (Azure AD) では、すべてのユーザーに既定のアクセス許可のセットが付与されます。 ユーザーのアクセスは、ユーザーの種類、ユーザーの[ロール メンバーシップ](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal)、および個々のオブジェクトの所有権で構成されます。 この記事では、これらの既定のアクセス許可について説明し、メンバーとゲスト ユーザーの既定値を比較します。

## <a name="member-and-guest-users"></a>メンバーとゲスト ユーザー
受け取る既定のアクセス許可のセットは、ユーザーがテナントのネイティブ メンバー (メンバー ユーザー) かどうか、またはユーザーが B2B コラボレーション ゲスト (ゲスト ユーザー) かどうかによって異なります。 B2B コラボレーションのゲスト ユーザーについて詳しくは、「[Azure AD B2B コラボレーションとは](../b2b/what-is-b2b.md)」をご覧ください。 
* メンバー ユーザーは、アプリケーションの登録、自分のプロファイル写真と携帯電話番号の管理、自分のパスワードの変更、B2B ゲストの招待を行うことができます。 さらに、すべてのディレクトリ情報を読むことができます (いくつか例外があります)。 
* Azure AD B2B ゲスト ユーザーは、ディレクトリ アクセス許可を制限されています。 たとえば、ゲスト ユーザーは、自分のプロファイル情報以外の情報をテナントから参照できません。 ただし、ゲスト ユーザーは、ユーザー プリンシパル名または objectId を提供することにより、別のユーザーに関する情報を取得できます。 ゲストは、グループやアプリケーションなど、他のテナント オブジェクトに関する情報を表示できません。

ゲストの既定のアクセス許可は、既定で制限されています。 ゲストを管理者ロールに追加することができ、追加すると、ロールに含まれる読み取りと書き込みのすべてのアクセス許可が付与されます。 もう 1 つ使用できる制限として、ゲスト ユーザーが他のゲストを招待する権限を制限できます。 **[ゲストは招待ができる]** を **[いいえ]** に設定すると、ゲストは他のゲストを招待できなくなります。 詳しくは、「[B2B コラボレーションの招待の委任](../b2b/delegate-invitations.md)」をご覧ください。 ゲスト ユーザーにメンバー ユーザーと同じアクセス許可を既定で付与するには、**[ゲストのアクセス許可を制限する]** を **[いいえ]** に設定します。 この設定は、メンバー ユーザーのすべてのアクセス許可をゲスト ユーザーに既定で付与し、ゲストが管理者ロールに追加されるのも許可します。

## <a name="compare-member-and-guest-default-permissions"></a>メンバーとゲストの既定のアクセス許可を比較する

**領域** | **メンバー ユーザーのアクセス許可** | **ゲスト ユーザーのアクセス許可**
------------ | --------- | ----------
ユーザーと連絡先 | ユーザーと連絡先のすべてのパブリック プロパティを読み取る<br>ゲストを招待する<br>自分のパスワードを変更する<br>自分の携帯電話番号を管理する<br>自分の写真を管理する<br>自分の更新トークンを無効にする | 自分のプロパティを読み取る<br>他のユーザーと連絡先の表示名、メール アドレス、サインイン名、写真、ユーザー プリンシパル名、ユーザーの種類の各プロパティを読み取る<br>自分のパスワードを変更する
グループ | セキュリティ グループを作成する<br>Office 365 グループを作成する<br>グループのすべてのプロパティを読み取る<br>非表示でないグループのメンバーシップを読み取る<br>参加しているグループの非表示 Office 365 グループのメンバーシップを読み取る<br>所有するグループのプロパティ、所有権、メンバーシップを管理する<br>所有するグループにゲストを追加する<br>動的メンバーシップの設定を管理する<br>所有するグループを削除する<br>所有する Office 365 グループを復元する | グループのすべてのプロパティを読み取る<br>非表示でないグループのメンバーシップを読み取る<br>参加しているグループの非表示 Office 365 グループのメンバーシップを読み取る<br>所有するグループを管理する<br>所有するグループにゲストを追加する (許可されている場合)<br>所有するグループを削除する<br>所有する Office 365 グループを復元する 
[アプリケーション] | 新しいアプリケーションを登録 (作成) する<br>登録済みアプリケーションとエンタープライズ アプリケーションのプロパティを読み取る<br>所有するアプリケーションのプロパティ、割り当て、資格情報を管理する<br>ユーザーのアプリケーション パスワードを作成または削除する<br>所有するアプリケーションを削除する<br>所有するアプリケーションを復元する | 登録済みアプリケーションとエンタープライズ アプリケーションのプロパティを読み取る<br>所有するアプリケーションのプロパティ、割り当て、資格情報を管理する<br>所有するアプリケーションを削除する<br>所有するアプリケーションを復元する
デバイス | デバイスのすべてのプロパティを読み取る<br>所有するデバイスのすべてのプロパティを管理する<br> | アクセス許可なし<br>所有するデバイスを削除する<br>
Directory | 会社のすべての情報を読み取る<br>すべてのドメインを読み取る<br>すべてのパートナー契約を読み取る | 表示名と検証済みドメインを読み取る
ロールとスコープ | すべての管理者ロールとメンバーシップを読み取る<br>管理単位のすべてのプロパティとメンバーシップを読み取る | アクセス許可なし 
サブスクリプション | すべてのサブスクリプションを読み取る<br>サービス プラン メンバーを有効にする | アクセス許可なし
ポリシー | ポリシーのすべてのプロパティを読み取る<br>所有するポリシーのすべてのプロパティを管理する | アクセス許可なし

## <a name="to-restrict-the-default-permissions-for-member-users"></a>メンバー ユーザーの既定のアクセス許可を制限するには

メンバー ユーザーの既定のアクセス許可は、次の方法で制限できます。

アクセス許可 | 設定の説明
---------- | ------------
セキュリティ グループを作成できる | このオプションを [いいえ] に設定すると、ユーザーはセキュリティ グループを作成できません。 その場合でも、全体管理者とユーザー アカウント管理者はセキュリティ グループを作成できます。 方法については、「[グループの設定を構成するための Azure Active Directory コマンドレット](../users-groups-roles/groups-settings-cmdlets.md)」をご覧ください。
Office 365 グループを作成できる | このオプションを [いいえ] に設定すると、ユーザーは Office 365 グループを作成できません。 このオプションを [一部] に設定すると、選ばれたユーザーのセットは Office 365 グループを作成できます。 その場合でも、全体管理者とユーザー アカウント管理者は Office 365 グループを作成できます。 方法については、「[グループの設定を構成するための Azure Active Directory コマンドレット](../users-groups-roles/groups-settings-cmdlets.md)」をご覧ください。
Azure AD 管理ポータルへのアクセスを制限する | このオプションを [いいえ] に設定すると、ユーザーは Azure Active Directory ポータルにアクセスできません。
他のユーザーを読み取ることができる | この設定は PowerShell のみでご利用いただけます。 これを $false に設定すると、管理者以外のすべてのユーザーはディレクトリからユーザー情報を読み取ることができなくなります。 Exchange Online などの他の Microsoft サービスのユーザー情報の読み取りは妨げられません。 この設定は特殊な状況を想定しているため、これを $false に設定することは推奨されません。

## <a name="object-ownership"></a>オブジェクトの所有権

### <a name="application-registration-owner-permissions"></a>アプリケーション登録所有者のアクセス許可
ユーザーがアプリケーションを登録すると、そのユーザーはアプリケーションの所有者として自動的に追加されます。 所有者は、名前やアプリが要求するアクセス許可など、アプリケーションのメタデータを管理できます。 また、SSO の構成やユーザーの割り当てなど、アプリケーションのテナント固有の構成も管理できます。 所有者は、他の所有者を追加または削除することもできます。 全体管理者とは異なり、所有者は、自分が所有するアプリケーションのみを管理できます。

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>グループ所有者のアクセス許可

グループを作成したユーザーは、そのグループの所有者として自動的に追加されます。 所有者は、名前などのグループのプロパティおよびメンバーシップを管理できます。 所有者は、他の所有者を追加または削除することもできます。 全体管理者およびユーザー アカウント管理者とは異なり、所有者が管理できるのは自分が所有するグループだけです。 グループ所有者の割り当てについては、「[グループの所有者の管理](active-directory-accessmanagement-managing-group-owners.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../../billing/billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
* [ユーザーの管理](add-users-azure-active-directory.md)
