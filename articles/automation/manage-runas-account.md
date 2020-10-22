---
title: Azure Automation の実行アカウントを管理する
description: この記事では、PowerShell または Azure portal を使用して、実行アカウントを管理する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0849eb0c421883ecb0510451ff81b604538c9cc3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069893"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを管理する

Azure Automation の実行アカウントでは、Automation の Runbook やその他の自動化機能を使用し、Azure Resource Manager または Azure クラシック デプロイ モデルのリソースを管理する認証を提供します。 この記事では、実行アカウントまたはクラシック実行アカウントを管理する方法について説明します。

自動化シナリオの処理に関連する Azure Automation アカウントの認証と説明については、「[Automation アカウントの認証の概要](automation-security-overview.md)」を参照してください。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>実行アカウントのアクセス許可

このセクションでは、通常の実行アカウントとクラシック実行アカウントの両方に対するアクセス許可を定義します。

実行アカウントを作成または更新するには、特定の特権およびアクセス許可が必要です。 Azure Active Directory のアプリケーション管理者とサブスクリプションの所有者は、すべてのタスクを完了できます。 職務が分離されている状況について、次の表にタスク、同等のコマンドレット、必要なアクセス許可の一覧を示します。

|タスク|コマンドレット  |最小限のアクセス許可  |アクセス許可を設定する場所|
|---|---------|---------|---|
|Azure AD アプリケーションを作成する|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | アプリケーション開発者ロール<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録] |
|資格情報をアプリケーションに追加します。|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | アプリケーション管理者または全体管理者<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|Azure AD サービス プリンシパルを作成および取得する|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | アプリケーション管理者または全体管理者<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|指定されたプリンシパルの Azure ロールを割り当てるまたは取得する|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | ユーザー アクセス管理者または所有者、あるいは次のアクセス許可がある:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [サブスクリプション](../role-based-access-control/role-assignments-portal.md)</br>[ホーム] > [サブスクリプション] > [\<subscription name\> - アクセス制御 (IAM)]|
|Automation の証明書を作成または削除する|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | リソース グループの共同作成者         |Automation アカウント リソース グループ|
|Automation の接続を作成または削除する|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|リソース グループの共同作成者 |Automation アカウント リソース グループ|

<sup>1</sup>[ユーザー設定] ページで Azure AD テナントの **[ユーザーはアプリケーションを登録できる]** オプションが **[はい]** に設定されている場合は、Azure AD テナントの管理者以外のユーザーが [AD アプリケーションを登録する](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)ことができます。 アプリケーション登録設定が **[いいえ]** の場合は、この操作を行うユーザーがこの表で定義されている必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者ロールに追加された場合は、ゲストとして追加されます。 このような状況では、`You do not have permissions to create…` 警告が **[Add Automation Account]\(Automation アカウントの追加\)** ページに表示されます。

グローバル管理者ロールが割り当てられたサブスクリプションの Active Directory インスタンスのメンバーである場合でも、 **[Automation アカウントの追加]** ページに `You do not have permissions to create…` 警告が表示されます。 この場合は、サブスクリプションの Active Directory インスタンスからの削除を要求してから、再追加するように要求し、Active Directory で完全なユーザーになるようにすることができます。

エラー メッセージが生成される状況が解決されたことを確認するには、次のようにします。

1. Azure portal の Azure Active Directory ペインで、 **[ユーザーとグループ]** を選択します。
2. **[すべてのユーザー]** を選択します。
3. ご自分の名前を選んでから、 **[プロファイル]** を選択します。
4. ユーザーのプロファイルの下にある **[ユーザーの種類]** 属性の値が、確実に **[ゲスト]** には設定されていないようにします。

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>クラシック実行アカウントの作成および構成に必要なアクセス許可

クラシック実行アカウントを構成または更新するには、サブスクリプション レベルの共同管理者ロールが必要です。 クラシック サブスクリプション アクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

## <a name="create-a-run-as-account-in-azure-portal"></a>Azure portal で実行アカウントを作成する

以下の手順を行って、Azure portal で Azure Automation アカウントを更新します。 実行およびクラシック実行アカウントを個別に作成します。 クラシック リソースを管理する必要がない場合は、Azure 実行アカウントのみを作成できます。

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure ポータルにログインします。

2. **Automation アカウント**を検索して選択します。

3. [Automation アカウント] ページで、一覧から Automation アカウントを選択します。

4. 左側のウィンドウの **[アカウント設定]** から **[実行アカウント]** を選択します。

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="[実行アカウント] オプションを選択する。":::

5. 必要なアカウントに応じて、 **[+ Azure 実行アカウント]** または **[+ Azure クラシック実行アカウント]** を使用します。 概要情報を確認した後、 **[作成]** クリックします。

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="[実行アカウント] オプションを選択する。":::

6. Azure によって実行アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。 アカウントが作成されていることを示すバナーも表示されます。 プロセスが完了するまでに数分かかることがあります。

## <a name="create-a-run-as-account-using-powershell"></a>PowerShell を使用して実行アカウントを作成する

提供されたスクリプトを使用して PowerShell で実行アカウントを作成する場合の要件一覧を次に示します。 これらの要件は、両方の種類の実行アカウントに適用されます。

* Azure Resource Manager モジュール 3.4.1 以降がインストールされた Windows 10 または Windows Server 2016。 PowerShell スクリプトでは、以前のバージョンの Windows はサポートされていません。
* Azure PowerShell 6.2.4 以降。 詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページを参照してください。
* Automation アカウント。`AutomationAccountName` および `ApplicationDisplayName` パラメーターの値として参照されます。
* [実行アカウントを構成するために必要なアクセス許可](#permissions)に関する記述で一覧表示されているものに相当するアクセス許可。

PowerShell スクリプトの必須パラメーターである `AutomationAccountName`、`SubscriptionId` および `ResourceGroupName`を取得するには、次の手順を行います。

1. Azure portal で、 **[Automation アカウント]** を選択します。

1. [Automation アカウント] ページで、ご自分の Automation アカウントを選択します。

1. アカウント設定セクションで、 **[プロパティ]** を選択します。

1. **[プロパティ]** ページの **[名前]** 、 **[サブスクリプション ID]** 、および **[リソース グループ]** の値をメモします。

   ![Automation アカウントの [プロパティ] ページ](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>実行アカウントを作成する PowerShell スクリプト

PowerShell スクリプトでは、いくつかの構成をサポートしています。

* 自己署名証明書を使用して実行アカウントを作成する。
* 自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* エンタープライズ証明機関 (CA) によって発行された証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

1. 次のコマンドを使用して、スクリプトをダウンロードし、ローカル フォルダーに保存します。

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. 管理者特権で PowerShell を起動し、スクリプトが格納されているフォルダーに移動します。

3. 次のいずれかのコマンドを実行して、自分の要件に基づいて実行アカウントまたはクラシック実行アカウントを作成します。

    * 自己署名証明書を使用して実行アカウントを作成する。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * 自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * エンタープライズ証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        エンタープライズ公開証明書 ( .cer ファイル) を使ってクラシック実行アカウントを作成した場合は、この証明書を使用します。 このスクリプトでは、PowerShell セッションの実行に使用したお使いのコンピューターのユーザー プロファイルの一時ファイル フォルダー `%USERPROFILE%\AppData\Local\Temp` にそれを作成して保存します。 [Azure portal への管理 API 証明書のアップロード](../cloud-services/cloud-services-configure-ssl-certificate-portal.md)に関するページを参照してください。

    * Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. スクリプトの実行後に、Azure での認証が求められます。 サブスクリプション管理者ロールのメンバーであるアカウントを使用して、サインインします。 クラシック実行アカウントを作成する場合、そのサブスクリプションの共同管理者のアカウントを使用する必要があります。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>実行アカウントまたはクラシック実行アカウントの削除

このセクションでは、実行またはクラシック実行アカウントを削除する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 実行アカウントを削除した場合、Azure portal または提供されている PowerShell スクリプトを使用してそれを再作成できます。

1. Azure Portal で Automation アカウントを開きます。

2. 左ペインで、アカウントの設定セクションの **[実行アカウント]** を選択します。

3. 実行アカウントのプロパティ ページで、削除する実行アカウントまたはクラシック実行アカウントを選択します。

4. 選択したアカウントの [プロパティ] ペインで、 **[削除]** をクリックします。

   ![Azure 実行アカウントを削除する](media/manage-runas-account/automation-account-delete-runas.png)

5. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>自己署名証明書を更新する

実行アカウント用に作成した自己署名証明書は、作成日から 1 年後に有効期限が切れます。 実行アカウントの有効期限が切れる前のある時点で、証明書を更新する必要があります。 有効期限が切れる前にいつでも更新することができます。

自己署名証明書を更新するときに、現在有効な証明書は保持されます。これにより、キューに登録されているかアクティブに実行されていて、実行アカウントで認証される Runbook は確実に悪影響を受けることがなくなります。 証明書は、有効期限日を迎えるまで存在し続けます。

>[!NOTE]
>実行アカウントが侵害されていると思われる場合は、自己署名証明書を削除してから再作成することができます。

>[!NOTE]
>エンタープライズ証明機関によって発行された証明書を使用するように実行アカウントを構成している場合に、自己署名証明書を更新するオプションを使用すると、エンタープライズ証明書は自己署名証明書に置き換えられます。

自己署名証明書を更新するには、次の手順を使用します。

1. Azure Portal で Automation アカウントを開きます。

1. アカウントの設定セクションで、 **[実行アカウント]** を選択します。

    ![Automation アカウントのプロパティ ウィンドウ](media/manage-runas-account/automation-account-properties-pane.png)

1. 実行アカウントのプロパティ ページで、証明書を更新する実行アカウントまたはクラシック実行アカウントを選択します。

1. 選択したアカウントのプロパティ ペインで、 **[証明書の更新]** をクリックします。

    ![実行アカウントの証明書を書き換える](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="limit-run-as-account-permissions"></a>実行アカウントのアクセス許可を制限する

Azure のリソースに対する Automation のターゲット設定を制御するために、[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) スクリプトを実行できます。 このスクリプトでは、カスタム ロールの定義を作成して使用するために、既存の実行アカウント サービス プリンシパルを変更します。 このロールには、[Key Vault](../key-vault/index.yml) を除くすべてのリソースへのアクセス許可があります。

>[!IMPORTANT]
>**Update-AutomationRunAsAccountRoleAssignments.ps1** スクリプトを実行した後、実行アカウントを使用して Key Vault にアクセスする Runbook が動作しなくなります。 スクリプトを実行する前に、アカウント内の Runbook で Azure Key Vault の呼び出しを確認する必要があります。 Azure Automation Runbook から Key Vault へのアクセスを有効にするには、[実行アカウントを Key Vault のアクセス許可に追加する](#add-permissions-to-key-vault)必要があります。

実行サービス プリンシパルで実行できる内容をさらに制限する必要がある場合は、カスタム ロール定義の `NotActions` 要素に他のリソースの種類を追加できます。 次の例では、`Microsoft.Compute/*` へのアクセスが制限されます。 このリソースの種類をロール定義の `NotActions` に追加すると、そのロールではどのコンピューティング リソースにもアクセスできなくなります。 ロール定義の詳細については、「[Azure リソースのロール定義の概要](../role-based-access-control/role-definitions.md)」を参照してください。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

実行アカウントで使用されるサービス プリンシパルが、共同作成者とカスタムのどちらのロールの定義にあるかを確認できます。

1. Automation アカウントに移動し、アカウントの設定セクションで **[実行アカウント]** を選択します。
2. **[Azure 実行アカウント]** を選択します。
3. **[ロール]** を選択し、使用されているロールの定義を見つけます。

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="[実行アカウント] オプションを選択する。" lightbox="media/manage-runas-account/verify-role-expanded.png":::

また、複数のサブスクリプションまたは Automation アカウントの実行アカウントで使用されるロールの定義を確認することができます。 これを行うには、PowerShell ギャラリーの [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) スクリプトを使用します。

### <a name="add-permissions-to-key-vault"></a>Key Vault へのアクセス許可を追加する

Key Vault と実行アカウントのサービス プリンシパルでカスタム ロールの定義が使用されているかどうかを、Azure Automation で確認できるようにすることができます。 次の手順が必要です。

* Key Vault にアクセス許可を付与します。
* アクセス ポリシーを設定します。

PowerShell ギャラリーの [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) スクリプトを使用して、実行アカウントに Key Vault へのアクセス許可を付与することができます。 Key Vault へのアクセス許可の設定について詳しくは、「[Key Vault アクセス ポリシーを割り当てる](../key-vault/general/assign-access-policy-powershell.md)」を参照してください。

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>実行アカウントの間違った構成に関する問題を解決する

実行またはクラシック実行アカウントに必要な構成項目の一部が削除されたか、初期セットアップ中に正しく作成されなかった可能性があります。 構成が間違っている可能性のあるインスタンスは、次のとおりです。

* 証明書資産
* 接続資産
* 共同作成者ロールから削除された実行アカウント
* Azure AD のサービス プリンシパルまたはアプリケーション

このような構成が間違っているインスタンスについては、Automation アカウントによって変更が検出され、そのアカウントの実行アカウントのプロパティ ペインに [*Incomplete*]\(不完全\) の状態が表示されます。

![実行アカウントの構成が不完全な状態](media/manage-runas-account/automation-account-runas-config-incomplete.png)

実行アカウントを選択すると、アカウントのプロパティ ペインに次のエラー メッセージが表示されます。

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

こうした実行アカウントの問題は、実行アカウントを削除してから再作成すればすぐに解決できます。

## <a name="next-steps"></a>次のステップ

* [アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)。
* [Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)。