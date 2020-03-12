---
title: PIM の使用を開始する - Azure Active Directory | Microsoft Docs
description: Azure portal で Azure AD Privileged Identity Management (PIM) を有効にして使用を開始する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399330"
---
# <a name="start-using-privileged-identity-management"></a>Privileged Identity Management の使用開始

Privileged Identity Management (PIM) を使用すると、Azure Active Directory (Azure AD) 組織内のアクセス権を管理、制御、および監視できます。 この範囲には、Azure リソース、Azure AD のほか、Office 365 や Microsoft Intune などのその他の Microsoft オンライン サービスへのアクセスが含まれます。

この記事では、Privileged Identity Management を有効にしてその使用を開始する方法について説明します。

## <a name="prerequisites"></a>前提条件

Privileged Identity Management を使用するには、次のライセンスのいずれかが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

詳細については、「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」を参照してください。

## <a name="first-person-to-use-pim"></a>PIM を使用する最初のユーザー

ディレクトリ内で Privileged Identity Management を使用する最初のユーザーには、そのディレクトリ内の [[セキュリティ管理者]](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) および [[特権ロール管理者]](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ロールが自動的に割り当てられます。 ユーザーの Azure AD ロールの割り当てを管理できるのは特権ロール管理者だけです。 さらに、初回の検出と割り当てを体験できる[セキュリティ ウィザード](pim-security-wizard.md)の実行を選択することもできます。

## <a name="enable-pim"></a>PIM を有効にする

ディレクトリ内で Privileged Identity Management の使用を開始するには、まず Privileged Identity Management を有効にする必要があります。

1. ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com/) にサインインします。

    ディレクトリに対して Privileged Identity Management を有効にするには、Microsoft アカウント (@outlook.com など) ではなく、組織アカウント (@yourdomain.com など) を持つグローバル管理者である必要があります。

1. **[すべてのサービス]** をクリックし、**Azure AD Privileged Identity Management** サービスを探します。

    ![[すべてのサービス] でのAzure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. [Privileged Identity Management Quickstart] (Privileged Identity Management のクイックスタート) をクリックして開きます。

1. 一覧で、 **[PIM を承認する]** をクリックします。

    ![Privileged Identity Management を有効にするために Privileged Identity Management に同意する](./media/pim-getting-started/consent-pim.png)

1. **[ID を確認]** をクリックし、Azure MFA で ID を確認します。 アカウントの選択が求められます。

    ![本人確認のためにアカウント ウィンドウを選択する](./media/pim-getting-started/pick-account.png)

1. 確認のためにさらに情報が必要な場合は、そのプロセスが示されます。 詳細については、「[2 段階認証のサポートを受ける](../user-help/multi-factor-authentication-end-user-troubleshoot.md)」を参照してください。

    ![組織が詳細情報を必要とする場合の [詳細情報が必要] ウィンドウ](./media/pim-getting-started/more-information-required.png)

    たとえば、電話確認の提供を求められる場合があります。

    ![連絡方法を確認する追加のセキュリティ確認ページ](./media/pim-getting-started/additional-security-verification.png)

1. 確認プロセスを完了したら、 **[同意]** ボタンをクリックします。

1. 表示されるメッセージで、 **[はい]** をクリックして Privileged Identity Management サービスに同意します。

    ![同意プロセスを完了するために Privileged Identity Management のメッセージに同意する](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD ロールを取得するために PIM にサインアップする

ディレクトリに対して Privileged Identity Management を有効にしたら、Azure AD ロールを管理するために Privileged Identity Management にサインアップする必要があります。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD roles]\(Azure AD ロール)** をクリックします。

    ![Azure AD ロールを取得するために Privileged Identity Management にサインアップする](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **[サインアップ]** をクリックします。

1. 表示されるメッセージで、 **[はい]** をクリックして、Azure AD ロールを管理するために Privileged Identity Management にサインアップします。

    ![Azure AD ロールのメッセージを取得するために Privileged Identity Management にサインアップする](./media/pim-getting-started/sign-up-pim-message.png)

    サインアップを完了すると、Azure AD オプションが有効になります。 場合によっては、ポータルを更新する必要があります。

    Privileged Identity Management で保護する Azure リソースを検出して選択する方法については、[Privileged Identity Management で管理する Azure リソースの検出](pim-resource-roles-discover-resources.md)に関するページを参照してください。

## <a name="navigate-to-your-tasks"></a>タスクへの移動

Privileged Identity Management が設定されたら、ID 管理タスクを開始できます。

![[タスク] と [管理] のオプションを示す Privileged Identity Management のナビゲーション ウィンドウ](./media/pim-getting-started/pim-quickstart-tasks.png)

| タスク + 管理 | 説明 |
| --- | --- |
| **自分のロール**  | 自分に割り当てられている適格でアクティブなロールの一覧が表示されます。 ここでは、割り当てられている適格なロールをアクティブにできます。 |
| **個人の要求** | 適格なロール割り当てのアクティブ化の保留中要求が表示されます。 |
| **申請の承認** | ディレクトリ内のユーザーによる適格なロールのアクティブ化要求のうち、自分が承認するものの一覧が表示されます。 |
| **アクセスのレビュー** | 自分に完了が割り当てられているアクティブなアクセス レビューが一覧表示されます (自分自身のアクセスをレビューするものと、他のユーザーのアクセスをレビューするものの両方)。 |
| **Azure AD ロール** | Azure AD ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |
| **Azure リソース** | Azure リソース ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>ダッシュボードに PIM タイルを追加する

Privileged Identity Management を簡単に開けるようにするには、Azure portal ダッシュボードに [Privileged Identity Management] タイルを追加する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[すべてのサービス]** をクリックし、**Azure AD Privileged Identity Management** サービスを探します。

    ![[すべてのサービス] でのAzure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. [Privileged Identity Management Quickstart] (Privileged Identity Management のクイックスタート) をクリックして開きます。

1. [Privileged Identity Management Quickstart] (Privileged Identity Management のクイックスタート) ブレードをダッシュボードにピン留めするには、 **[ダッシュボードにブレードをピン留め]** をオンにします。

    ![[Privileged Identity Management] ブレードをダッシュボードにピン留めする画びょうアイコン](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure ダッシュボードでは、タイルが次のように表示されます。

    ![ダッシュボード上の [Privileged Identity Management Quickstart] (Privileged Identity Management のクイックスタート) タイル](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management で管理する Azure リソースを検出する](pim-resource-roles-discover-resources.md)
