---
title: グループに秘密度ラベルを割り当てる - Azure AD | Microsoft Docs
description: グループを自動的に設定するメンバーシップ ルールと、ルール参照を作成する方法。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/19/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07859299805c5f7be869350adbdbfa675775888c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404799"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Azure Active Directory で Office 365 グループに秘密度ラベルを割り当てる (プレビュー)

Azure Active Directory (Azure AD) では、[Microsoft 365 コンプライアンス センター](https://sip.protection.office.com/homepage)によって公開されている秘密度ラベルを Office 365 グループに適用することがサポートされています。 秘密度ラベルは、Outlook、Microsoft Teams、SharePoint などのサービス全体で、グループに対して適用されます。 現在、この機能はパブリック プレビュー段階にあります。

> [!IMPORTANT]
> Office 365 グループに Azure AD 秘密度ラベルを使用するには、Azure Active Directory Premium P1 ライセンスが必要です。

## <a name="group-settings-controlled-by-labels"></a>ラベルによって制御されるグループ設定

ラベルには、次の 2 つの設定を関連付けることができます。

- **プライバシー**:管理者は、プライバシー設定をラベルに関連付けて、グループがパブリックであるかプライベートであるかを制御できます。
- **ゲスト アクセス**:管理者は、そのラベルが割り当てられているすべてのグループに対してゲスト ポリシーを適用できます。 このポリシーは、ゲストをメンバーとして追加できるかどうかを指定します。 ゲスト ポリシーがラベルに対して構成されている場合、そのラベルを割り当てるグループでは AllowToAddGuests 設定の変更が許可されません。

## <a name="enable-sensitivity-label-support-in-powershell"></a>PowerShell で秘密度ラベルのサポートを有効にする

公開されているラベルをグループに適用するには、まずこの機能を有効にする必要があります。 次の手順を実行すると、Azure AD でこの機能が有効になります。

1. コンピューターで Windows PowerShell ウィンドウを開きます。 これは昇格された特権がなくても開くことができます。
1. 次のコマンドを実行して、コマンドレットを実行する準備をします。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    **[アカウントにサインインする]** ページで、管理者アカウントとパスワードを入力してサービスに接続し、 **[サインイン]** を選択します。
1. Azure AD 組織の現在のグループ設定を取得します。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > この Azure AD 組織に対してグループ設定が作成されていない場合は、まず設定を作成する必要があります。 「[グループの設定を構成するための Azure Active Directory コマンドレット](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets)」の手順に従って、この Azure AD 組織のグループ設定を作成します。

1. 次に、現在のグループ設定を表示します。

    ```PowerShell
    $Setting.Values
    ```

1. 次に、この機能を有効にします。

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 次に、変更を保存し、設定を適用します。

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

これで終了です。 機能が有効になったので、公開されているラベルをグループに適用できます。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Azure portal で新しいグループにラベルを割り当てる

1. [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選択し、 **[新しいグループ]** を選択します。
1. **[新しいグループ]** ページで、 **[Office 365]** を選択し、新しいグループに必要な情報を入力して、一覧から秘密度ラベルを選択します。

   ![[新しいグループ] ページで秘密度ラベルを割り当てる](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 変更を保存し、 **[作成]** を選択します。

グループが作成され、選択したラベルに関連付けられているポリシーが自動的に適用されます。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Azure portal で既存のグループにラベルを割り当てる

1. グローバル管理者かグループ管理者のアカウントを使用するか、またはグループの所有者として [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてのグループ]** ページから、ラベルを適用するグループを選択します。
1. 選択したグループのページで **[プロパティ]** を選択し、一覧から秘密度ラベルを選択します。

   ![グループの [概要] ページで秘密度ラベルを割り当てる](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. **[保存]** を選択して変更を保存します。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Azure portal で既存のグループからラベルを削除する

1. グローバル管理者かグループ管理者のアカウントを使用するか、またはグループの所有者として [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてのグループ]** ページから、ラベルを削除するグループを選択します。
1. その **[グループ]** ページで、 **[プロパティ]** を選択します。
1. **[削除]** を選択します。
1. **[保存]** を選択して変更を保存します。

## <a name="office-365-app-support-for-sensitivity-labels"></a>Office 365 アプリでの秘密度ラベルのサポート

次の Office 365 アプリとサービスは、このプレビューの秘密度ラベルをサポートしています。

- Azure AD 管理センター
- Microsoft 365 コンプライアンス センター
- SharePoint
- Outlook on the web
- Teams
- SharePoint 管理センター

Office 365 アプリのサポートの詳細については、[Office 365 での秘密度ラベルのサポート](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-new-sensitivity-labels)に関する記事を参照してください。

## <a name="using-classic-azure-ad-classifications"></a>従来の Azure AD 分類を使用する

この機能を有効にすると、新しいグループに対する "従来の" 分類は Office 365 でサポートされなくなります。 従来の分類とは、Azure AD PowerShell で `ClassificationList` 設定の値を定義することによって設定した以前の分類のことです。 この機能を有効にすると、それらの分類はグループに適用されなくなります。

## <a name="troubleshooting-issues"></a>問題のトラブルシューティング

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>グループの割り当てに秘密度ラベルを使用できない

[秘密度ラベル] オプションは、次のすべての条件が満たされている場合にのみ、グループに対して表示されます。

1. Microsoft 365 コンプライアンス センターでこのテナントに対してラベルが公開されている。
1. 機能が有効になっており、PowerShell で EnableMIPLabels が True に設定されている。
1. そのグループが Office 365 グループである。
1. そのテナントに、アクティブな Azure Active Directory Premium P1 ライセンスがある。
1. 現在サインインしているユーザーが、公開されているラベルにアクセスできる。
1. 現在サインインしているユーザーに、ラベルを割り当てるための十分な権限がある。 そのユーザーは、全体管理者、グループ管理者、またはグループの所有者のいずれかである必要があります。
1. 現在サインインしているユーザーに、Office 365 ライセンスが割り当てられている。 ライセンス要件の詳細については、「[Office アプリの機密ラベル](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-office-apps)」を参照してください。

ラベルをグループに割り当てるには、上記のすべての条件が満たされていることを確認してください。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>割り当てたいラベルが一覧にありません

探しているラベルが一覧にない場合は、次のいずれかの理由が考えられます。

- そのラベルが、Microsoft 365 コンプライアンス センターで公開されていない。 これは、公開されなくなったラベルにも当てはまります。 詳細については、管理者にご確認ください。
- ラベルは公開されているが、サインインしているユーザーにはそのラベルを使用できない。 ラベルにアクセスする方法の詳細については、管理者にご確認ください。

### <a name="how-can-i-change-the-label-on-a-group"></a>グループに対するラベルを変更するにはどうすればよいですか?

ラベルは、次に示すように、既存のグループに割り当てるのと同じ手順を使用することによりいつでも交換できます。

1. グローバル管理者かグループ管理者のアカウントを使用するか、またはグループの所有者として [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選びます。
1. **[すべてのグループ]** ページから、ラベルを適用するグループを選択します。
1. 選択したグループのページで **[プロパティ]** を選択し、一覧から新しい秘密度ラベルを選択します。
1. **[保存]** を選択します。

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>公開されているラベルに加えたグループ設定変更が、グループに対して更新されない

ベスト プラクティスとして、ラベルをグループに適用した後で、そのラベルのグループ設定を変更することはお勧めしません。 公開されたラベルに関連付けられているグループ設定を [Microsoft 365 コンプライアンス センター](https://sip.protection.office.com/homepage)で変更しても、それらのポリシー変更は影響を受けるグループに自動的には適用されません。

変更が必要な場合は、[Azure AD PowerShell スクリプト](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)を使用して、影響を受けるグループに更新を手動で適用します。 この方法を使用すると、既存のすべてのグループに新しい設定が適用されます。

## <a name="next-steps"></a>次の手順

- [Microsoft Teams、Office 365 グループ、SharePoint サイトで機密ラベルを使用する](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Azure AD PowerShell スクリプトを使用して、ラベルのポリシーを手動で変更した後にグループを更新する](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [グループの設定を編集する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [PowerShell コマンドを使用してグループを管理する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
