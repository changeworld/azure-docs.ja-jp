---
title: Azure IoT ソリューション アクセラレータと Azure Active Directory | Microsoft Docs
description: Azure IoT ソリューション アクセラレータ が Azure Active Directory を使用してアクセス許可を管理する方法を説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 676d5e553e2929ae09d447141ca315fd1cc448e3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448952"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>azureiotsolutions.com サイトでのアクセス許可

## <a name="what-happens-when-you-sign-in"></a>サインイン時に行われること

ユーザーが [azureiotsuite.com][lnk-azureiotsolutions] に初めてサインインすると、選択されている Azure Active Directory (AAD) テナントと Azure サブスクリプションに基づいて、ユーザーのアクセス許可レベルが決定されます。

1. サイトは最初に、ログイン ユーザー名の隣に表示されるテナントの一覧を設定するため、Azure からユーザーが属している AAD テナントを取得します。 現時点では、サイトは一度に 1 つのテナントのユーザー トークンしか取得できません。 このため、ユーザーが右上隅のドロップダウンを使用してテナントを切り替えると、サイトはユーザーをそのテナントにログインさせて、そのテナントのトークンを取得します。

2. 次に、サイトは、ユーザーが選択されているテナントと関連付けているサブスクリプションを Azure から検索します。 ユーザーが新しいソリューション アクセラレータを作成するときに、使用できるサブスクリプションが表示されます。

3. 最後に、サイトはサブスクリプション内およびソリューション アクセラレータというタグが付いているリソース グループ内のすべてのリソースを取得して、ホーム ページのタイトルを設定します。

以下のセクションでは、ソリューション アクセラレータへのアクセスを制御するロールについて説明します。

## <a name="aad-roles"></a>AAD のロール

AAD のロールは、ソリューション アクセラレータをプロビジョニングし、ソリューション アクセラレータ内のユーザーや一部の Azure サービスを管理する権限を制御します。

ADD の管理者ロールの詳細については、「[Azure AD での管理者ロールの割り当て][lnk-aad-admin]」を参照してください。 この記事では、主にソリューション アクセラレータで使用される**グローバル管理者**ロールと**ユーザー** ディレクトリ ロールに重点を置いて説明しています。

### <a name="global-administrator"></a>全体管理者

AAD テナントごとに多数のグローバル管理者がいてもかまいません。

* AAD テナントを作成したユーザーは、既定でそのテナントのグローバル管理者になります。
* 全体管理者は、基本と標準のソリューション アクセラレータをプロビジョニングできます (基本的なデプロイは単一の Azure 仮想マシンを使います)。

### <a name="domain-user"></a>ドメイン ユーザー

AAD テナントごとに多数のドメイン ユーザーがいてもかまいません。

* ドメイン ユーザーは、[azureiotsolutions.com][lnk-azureiotsolutions] サイトを使用して基本的なソリューション アクセラレータをプロビジョニングできます。
* ドメイン ユーザーは CLI を使用して基本的なソリューション アクセラレータを作成できます。

### <a name="guest-user"></a>ゲスト ユーザー

AAD テナントごとに多数のゲスト ユーザーがいてもかまいません。 ゲスト ユーザーの AAD テナントでの権限セットは制限されています。 したがって、ゲスト ユーザーは AAD テナントでソリューション アクセラレータをプロビジョニングできません。

AAD におけるユーザーとロールの詳細については、次のリソースを参照してください。

* [Azure AD でユーザーを作成する][lnk-create-edit-users]
* [ユーザーをアプリに割り当てる][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure サブスクリプション管理者ロール

Azure 管理者ロールは、Azure サブスクリプションを AAD テナントにマップする機能を制御します。

Azure 管理者ロールの詳細については、[Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法][lnk-admin-roles]に関する記事を参照してください。

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>サービス管理者が自分のサブスクリプションと特定の AAD テナントの間のディレクトリ マッピングを変更する必要がある場合は、 どうすればよいですか

「[既存のサブスクリプションを Azure AD ディレクトリに追加する方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)」をご覧ください

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>組織アカウントでログインするときにサービス管理者または共同管理者を変更するにはどうすればよいですか

[組織アカウントでログインしたときのサービス管理者および共同管理者の変更][lnk-service-admins]に関するサポート記事をご覧ください。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>次のエラーが表示されるのはなぜですか。 「お使いのアカウントにはソリューションを作成する適切なアクセス許可がありません。 アカウント管理者に確認するか、別のアカウントを使用してください。」

次の図を見てみましょう。

![][img-flowchart]

> [!NOTE]
> 自分が AAD テナントの全体管理者になっていることとサブスクリプションの共同管理者になっていることを確認した後もエラーが表示される場合は、ユーザーを削除してから必要なアクセス許可を再度割り当てるよう、アカウント管理者に依頼してください。 その場合はまず、ユーザーを全体管理者として追加したうえで、Azure サブスクリプションの共同管理者としてそのユーザーを追加することになります。 問題が解決しない場合は、[ヘルプとサポート][lnk-help-support]にお問い合わせください。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure サブスクリプションがあるのに次のエラーが表示されるのはなぜですか。 「構成済みソリューションを作成するには Azure サブスクリプションが必要です。 数分で無料試用版のアカウントを作成することができます。」

Azure サブスクリプションが確かにある場合は、サブスクリプションのテナント マッピングを調べ、ドロップダウンで正しいテナントが選択されていることを確認してください。 目的のテナントが正しいことを確認できた場合は、上の図に従って、サブスクリプションとこの AAD テナントのマッピングを確認してください。

## <a name="next-steps"></a>次の手順
IoT Suite ソリューション アクセラレータについて引き続き学習するには、[ソリューション アクセラレータをカスタマイズする][lnk-customize]方法を参照してください。

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
