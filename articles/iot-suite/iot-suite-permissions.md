---
title: "Azure IoT Suite と Azure Active Directory | Microsoft Docs"
description: "Azure IoT Suite が Azure Active Directory を使用してアクセス許可を管理する方法を説明します。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: ee7acd393539277a5bc23a6fed40d07961974b9a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>azureiotsuite.com サイトでのアクセス許可

## <a name="what-happens-when-you-sign-in"></a>サインイン時に行われること

ユーザーが [azureiotsuite.com][lnk-azureiotsuite] に初めてサインインすると、選択されている Azure Active Directory (AAD) テナントと Azure サブスクリプションに基づいて、ユーザーのアクセス許可レベルが決定されます。

1. サイトは最初に、ログイン ユーザー名の隣に表示されるテナントの一覧を設定するため、Azure からユーザーが属している AAD テナントを取得します。 現時点では、サイトは一度に 1 つのテナントのユーザー トークンしか取得できません。 このため、ユーザーが右上隅のドロップダウンを使用してテナントを切り替えると、サイトはユーザーをそのテナントにログインさせて、そのテナントのトークンを取得します。

2. 次に、サイトは、ユーザーが選択されているテナントと関連付けているサブスクリプションを Azure から検索します。 ユーザーが新しい構成済みソリューションを作成するときに、使用できるサブスクリプションが表示されます。

3. 最後に、サイトはサブスクリプション内および構成済みソリューションというタグが付いているリソース グループ内のすべてのリソースを取得して、ホーム ページのタイトルを設定します。

以下のセクションでは、構成済みソリューションへのアクセスを制御するロールについて説明します。

## <a name="aad-roles"></a>AAD のロール

AAD のロールは、構成済みソリューションをプロビジョニングし、構成済みソリューション内のユーザーを管理する権限を制御します。

ADD の管理者ロールの詳細については、「[Azure AD での管理者ロールの割り当て][lnk-aad-admin]」を参照してください。 この記事では、主に構成済みソリューションで使用される**グローバル管理者**ロールと**ユーザー** ディレクトリ ロールに重点を置いて説明しています。

### <a name="global-administrator"></a>グローバル管理者

AAD テナントごとに多数のグローバル管理者がいてもかまいません。

* AAD テナントを作成したユーザーは、既定でそのテナントのグローバル管理者になります。
* グローバル管理者は、基本的かつ標準の構成済みソリューションをプロビジョニングできます。

### <a name="domain-user"></a>ドメイン ユーザー

AAD テナントごとに多数のドメイン ユーザーがいてもかまいません。

* ドメイン ユーザーは [azureiotsuite.com][lnk-azureiotsuite] サイトを使用して基本的な構成済みソリューションをプロビジョニングできます。
* ドメイン ユーザーは CLI を使用して基本的な構成済みソリューションを作成できます。

### <a name="guest-user"></a>ゲスト ユーザー

AAD テナントごとに多数のゲスト ユーザーがいてもかまいません。 ゲスト ユーザーの AAD テナントでの権限セットは制限されています。 したがって、ゲスト ユーザーは AAD テナントで構成済みソリューションをプロビジョニングできません。

AAD におけるユーザーとロールの詳細については、次のリソースを参照してください。

* [Azure AD でユーザーを作成する][lnk-create-edit-users]
* [ユーザーをアプリに割り当てる][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure サブスクリプション管理者ロール

Azure 管理者ロールは、Azure サブスクリプションを AD テナントにマップする機能を制御します。

Azure 管理者ロールの詳細については、[Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法][lnk-admin-roles]に関する記事を参照してください。

## <a name="faq"></a>FAQ

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>サービス管理者が自分のサブスクリプションと特定の AAD テナントの間のディレクトリ マッピングを変更する必要がある場合は、 どうすればよいですか

「[既存のサブスクリプションを Azure AD ディレクトリに追加する方法](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)」をご覧ください

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>組織アカウントでログインするときにサービス管理者または共同管理者を変更するにはどうすればよいですか

「[組織アカウントを利用してログインしたときのサービス管理者および共同管理者の変更][lnk-service-admins]」を参照してください。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>次のエラーが表示されるのはなぜですか。 「お使いのアカウントにはソリューションを作成する適切なアクセス許可がありません。 アカウント管理者に確認するか、別のアカウントを使用してください。」

次の図を見てみましょう。

![][img-flowchart]

> [!NOTE]
> 自分が AAD テナントの全体管理者になっていることとサブスクリプションの共同管理者になっていることを確認した後もエラーが表示される場合は、ユーザーを削除してから必要なアクセス許可を再度割り当てるよう、アカウント管理者に依頼してください。 その場合はまず、ユーザーを全体管理者として追加したうえで、Azure サブスクリプションの共同管理者としてそのユーザーを追加することになります。 問題が解決しない場合は、[ヘルプとサポート][lnk-help-support]にお問い合わせください。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Azure サブスクリプションがあるのに次のエラーが表示されるのはなぜですか。 「構成済みソリューションを作成するには Azure サブスクリプションが必要です。 数分で無料試用版のアカウントを作成することができます。」

Azure サブスクリプションが確かにある場合は、サブスクリプションのテナント マッピングを調べ、ドロップダウンで正しいテナントが選択されていることを確認してください。 目的のテナントが正しいことを確認できた場合は、上の図に従って、サブスクリプションとこの AAD テナントのマッピングを確認してください。

## <a name="next-steps"></a>次のステップ
IoT Suite について引き続き学習するには、[構成済みソリューションをカスタマイズする][lnk-customize]方法を参照してください。

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
