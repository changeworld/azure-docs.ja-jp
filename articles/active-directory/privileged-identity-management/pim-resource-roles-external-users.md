---
title: PIM で外部ユーザーを招待して Azure リソース ロールを割り当てる | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で外部ユーザーを招待して Azure リソース ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 561fccd3e0baee481937954cc65601576a4fd60f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730554"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>PIM で外部ユーザーを招待して Azure リソース ロールを割り当てる

Azure Activity Directory (Azure AD) 企業間に取引 (B2B) は、組織が任意のアカウントを使用して外部ユーザーや仕入先と共同作業できるようにする、Azure AD 内の一連の機能です。 B2B と Azure AD Privileged Identity Management (PIM) を組み合わせると、コンプライアンスとガバナンス要件を外部ユーザーに継続的に適用できます。 たとえば、次の Azure リソース用の PIM 機能を外部ユーザーとともに使用できます。

- 特定の Azure リソースにアクセスを割り当てる
- ジャストインタイム アクセスを有効にする
- 割り当て期間と終了日を指定する
- アクティブな割り当てまたはライセンス認証に MFA を要求する
- アクセス レビューを実行する
- アラートや監査ログを使用する

この記事では、PIM を使用して、ディレクトリに外部ユーザーを招待し、Azure リソースへのアクセスを管理する方法について説明します。

## <a name="when-would-you-invite-external-users"></a>いつ外部ユーザーを招待しますか?

次に示すのは、ディレクトリに外部ユーザーを招待するいくつかのシナリオの例です。

- プロジェクトの Azure リソースにアクセスするために、電子メール アカウントしか持っていない外部の自営仕入先を許可します。
- オンプレミスの Active Directory フェデレーション サービスを使用する大規模な組織で、経費アプリケーションにアクセスすることを外部のパートナーを許可します。
- 組織外のサポート エンジニア (Microsoft サポートなど) に、問題をトラブルシューティングしてもらうために Azure リソースに一時的にアクセスすることを許可します。

## <a name="how-does-external-collaboration-using-b2b-work"></a>外部コラボレーションでは B2B 作業をどのように使用しましたか?

B2B を使用する場合は、ディレクトリに外部ユーザーを招待できます。 外部ユーザーはディレクトリに表示されますが、そのユーザーには関連付けられている資格情報はありません。 外部ユーザーが認証される必要がある場合はいつでも、お客様のディレクトリではなく外部ユーザー自身のホーム ディレクトリで認証される必要があります。 つまり、外部ユーザーが自分のホーム ディレクトリにアクセスできなくなった場合には、ディレクトリへのアクセスを自動的に失います。 たとえば、外部ユーザーが自分の組織を離れた場合、ディレクトリ内でそれらの外部ユーザーと共有しているリソースへのアクセスをその外部ユーザーは自動的に失い、お客様は何もする必要がありません。 B2B の詳細については、「[Azure Active Directory B2B のゲスト ユーザー アクセスとは](../b2b/what-is-b2b.md)」を参照してください。

![B2B と外部ユーザー](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>外部コラボレーション設定の確認

ディレクトリに外部ユーザーを招待できるかを確認するには、外部コラボレーション設定を確認する必要があります。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. **[Azure Active Directory]** > **[ユーザー設定]** の順にクリックします。

1. **[外部コラボレーションの設定]** をクリックします。

    ![外部コラボレーションの設定](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **「管理者とゲスト招待元ロールのユーザーは招待ができる」** スイッチが **[はい]** に設定されていることを確認します。

## <a name="invite-an-external-user-and-assign-a-role"></a>外部ユーザーを招待して役割を割り当てる

PIM を使用して、メンバー ユーザーと同様に、外部ユーザーを招待し、Azure リソース ロールに資格を付与できます。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)または[ユーザー アカウント管理者](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** をクリックします。

1. **[リソース フィルター]** を使用して、管理対象リソースの一覧をフィルター処理します。

1. リソース、リソース グループ、サブスクリプション、管理グループなど、管理するリソースをクリックします。

    スコープは外部ユーザーが必要とするものにのみ設定してください。

1. [管理] の **[ロール]** をクリックして、Azure リソースのロール一覧を表示します。

    ![Azure リソース ロール](./media/pim-resource-roles-external-users/resources-roles.png)

1. ユーザーが必要とする最小限のロールをクリックします。

    ![選択されたロール](./media/pim-resource-roles-external-users/selected-role.png)

1. ロール ページで、**[メンバーの追加]** をクリックして [新しい割り当て] ウィンドウを開きます。

1. **[メンバーまたはグループの選択]** をクリックします。

    ![メンバーまたはグループの選択](./media/pim-resource-roles-external-users/select-member-group.png)

1. 外部ユーザーを招待して、**[招待]** をクリックします。

    ![ゲストを招待する](./media/pim-resource-roles-external-users/invite-guest.png)

1. 外部ユーザーを指定した後に、**[招待]** をクリックします。

    外部ユーザーは、選択したメンバーとして追加する必要があります。

1. [メンバーまたはグループの選択] ウィンドウで、**[選択]** をクリックします。

1. [メンバーシップ設定] ウィンドウで、割り当ての種類と期間を選択します。

    ![メンバーシップ設定](./media/pim-resource-roles-external-users/membership-settings.png)

1. 割り当てを完了するには、**[完了]**、次に **[追加]** をクリックします。

    外部ユーザーのロールの割り当ては、ロールのリストに表示されます。

    ![外部ユーザーのロールの割り当て](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>外部ユーザーとしてロールをアクティブ化します。

外部のユーザーであれば、まず Azure AD ディレクトリへの招待を受け入れて、場合によってはロールをアクティブ化する必要があります。

1. ディレクトリ招待の電子メールを開きます。 電子メールは次のような内容です。

    ![電子メールの招待](./media/pim-resource-roles-external-users/email-invite.png)

1. 電子メールの **[開始する]** リンクをクリックします。

1. アクセス許可を確認した後に、**[同意する]** をクリックします。

    ![アクセス許可の確認](./media/pim-resource-roles-external-users/invite-accept.png)

1. 利用規約に同意し、サインインしたままにするかどうかを指定するように求められる場合があります。

    Azure Portal が開きます。 ロールに資格があるだけでは、リソースにはアクセスできません。

1. ロールをアクティブ化するには、ロールをアクティブ化するリンクがある電子メールを開きます。 電子メールは次のような内容です。

    ![電子メールの招待](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. PIM で、**[ロールのアクティブ化]** をクリックして資格のあるロールを開きます。

    ![自分のロール - 資格がある](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. [アクション] の下の **[アクティブ化]** リンクをクリックします。

    ロールの設定に応じて、ロールをアクティブ化するためのいくらかの情報を指定する必要があります。

1. ロールの設定を指定したら、**[アクティブ化]** をクリックしてロールをアクティブ化します。

    ![ロールをアクティブ化する](./media/pim-resource-roles-external-users/activate-role.png)

    管理者が要求を承認する必要がない限り、指定されたリソースへのアクセスが必要です。

## <a name="view-activity-for-an-external-user"></a>外部ユーザーのアクティビティの表示

メンバー ユーザーと同じように、監査ログを表示して、外部ユーザーが行っていることを追跡できます。

1. 管理者は、PIM を開き、共有されているリソースを選択します。

1. **[リソースの監査]** をクリックして、そのリソースのアクティビティを表示します。 リソース グループのアクティビティの例を次に示します。

    ![リソースの監査](./media/pim-resource-roles-external-users/audit-resource.png)

1. 外部ユーザーのアクティビティを表示するには、**[Azure Active Directory]** > **[ユーザー]** > 外部ユーザーの順にクリックします。

1. **[監査ログ]** をクリックして、ディレクトリの監査ログを表示します。 必要に応じて、フィルターを指定できます。

    ![ディレクトリ監査](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ディレクトリ ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Azure Active Directory B2B のゲスト ユーザー アクセスとは](../b2b/what-is-b2b.md)
