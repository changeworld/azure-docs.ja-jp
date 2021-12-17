---
title: Azure Virtual Desktop の作業の開始機能に関するトラブルシューティング
description: Azure Virtual Desktop の作業の開始機能に関する問題のトラブルシューティング方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21cdccf5a025db4516d53c506c070fcf6f580fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780681"
---
# <a name="troubleshoot-the-azure-virtual-desktop-getting-started-feature"></a>Azure Virtual Desktop の作業の開始機能のトラブルシューティング

Azure Virtual Desktop の作業の開始機能では、入れ子になったテンプレートを使用して、Azure Virtual Desktop での検証や自動化に使用する Azure リソースをデプロイします。 作業の開始機能では、実行されているサブスクリプションに既存の Active Directory Domain Services (AD DS) または Azure Active Directory Domain Services (Azure AD DS) があるかどうかに基づいて、2 つまたは 3 つのリソース グループが作成されます。 すべてのリソース グループは、同じユーザー定義のプレフィックスで始まります。

入れ子になったテンプレートを実行すると、3 つのリソース グループと、Azure Resource Manager リソースをプロビジョニングするテンプレートが作成されます。 次の一覧は、各リソース グループとそれによって実行されるテンプレートを示しています。

"-deployment" で終わるリソース グループは、次のテンプレートを実行します。

- easy-button-roleassignment-job-linked-template
- easy-button-prerequisitecompletion-job-linked-template
- easy-button-prerequisite-job-linked-template
- easy-button-inputvalidation-job-linked-template
- easy-button-deploymentResources-linked-template
- easy-button-prerequisite-user-setup-linked-template

>[!NOTE]
>easy-button-prerequisite-user-setup-linked-template は省略可能であり、検証ユーザーを作成した場合にのみ表示されます。

"-wvd" で終わるリソース グループは、次のテンプレートを実行します。

- NSG-linkedTemplate
- vmCreation-linkedTemplate
- Workspace-linkedTemplate
- wvd-resources-linked-template
- easy-button-wvdsetup-linked-template

"-prerequisite" で終わるリソース グループは、次のテンプレートを実行します。

- easy-button-prerequisite-resources-linked-template

>[!NOTE]
>このリソース グループは省略可能であり、サブスクリプションに Azure AD DS または AD DS がない場合にのみ表示されます。

## <a name="no-subscriptions"></a>サブスクリプションなし

この問題では、作業の開始機能を開くと、"サブスクリプションなし" というエラー メッセージが表示されます。 このエラーは、アクティブな Azure サブスクリプションがない状態で機能を開こうとした場合に発生します。

この問題を解決するには、サブスクリプションまたは影響を受けているユーザーがアクティブな Azure サブスクリプションを持っているかどうかを確認します。 ない場合は、ユーザーのサブスクリプションで、所有者ロールベースのアクセス制御 (RBAC) ロールを割り当てます。

## <a name="you-dont-have-permissions"></a>アクセス許可がない

この問題は、作業の開始機能を開いて、"You don't have permissions" (アクセス許可がありません) というエラー メッセージが表示された場合に発生します。 このメッセージは、機能を実行しているユーザーに、アクティブな Azure サブスクリプションに対する所有者のアクセス許可が付与されていない場合に表示されます。

この問題を解決するには、所有者のアクセス許可を持つ Azure アカウントでサインインし、影響を受けるアカウントに所有者 RBAC ロールを割り当てます。

## <a name="fields-under-virtual-machine-tab-are-grayed-out"></a>仮想マシン タブのフィールドが淡色表示されている

この問題は、 **[仮想マシン]** タブを開いた時に、"Do you want users to share this machine?" (ユーザーにこのマシンを共有させますか?) のフィールドが淡色表示されていると発生します。この問題が発生すると、イメージ タイプの変更、使用するイメージの選択、および VM サイズの変更ができなくなります。

この問題は、デプロイを開始するために既に使用されていたプレフィックスを使用して機能を実行すると発生します。 この機能によってデプロイが作成されると、Azure でのデプロイを表すオブジェクトが作成されます。 イメージなどのオブジェクト内の一部の値は、そのオブジェクトにアタッチされ、複数のオブジェクトで同じイメージが使用されることを防ぎます。

この問題を解決するには、既存のプレフィックスを持つすべてのリソース グループを削除するか、新しいプレフィックスを使用します。

## <a name="username-must-not-include-reserved-words"></a>ユーザー名には予約語を使用しないでください

この問題は、作業の開始機能で、フィールドに入力した新しいユーザー名が受理されない場合に発生します。

このエラー メッセージは、Azure でパブリック エンドポイントのユーザー名に特定の単語を使用することが許可されていないために表示されます。 ブロックされている単語の包括的な一覧については、「[予約されたリソース名のエラーを解決する](../azure-resource-manager/templates/error-reserved-resource-name.md)」を参照してください。

この問題を解決するには、新しい単語を試すか、一意になるようブロックされた単語に文字を追加してください。 たとえば、"admin" という単語がブロックされた場合は、代わりに "AVDadmin" を使用してみてください。

## <a name="the-value-must-be-between-12-and-72-characters-long"></a>値の長さは 12 から 72 文字にする必要があります

このエラー メッセージは、パスワードを入力する際に、文字数が長すぎる、または短すぎることにより、必要な文字数の条件が満たされていない場合に表示されます。 Azure のパスワードの長さと複雑さの要件は、後に Windows で使用するフィールドにも適用されますが、この要件はそれほど厳しくありません。

この問題を解決するには、[Microsoft のパスワード ガイドライン](https://www.microsoft.com/research/publication/password-guidance)に従っているアカウントを使用するか、[Azure AD パスワード保護](../active-directory/authentication/concept-password-ban-bad.md)を使用するようにしてください。

## <a name="error-messages-for-easy-button-prerequisite-user-setup-linked-template"></a>easy-button-prerequisite-user-setup-linked-template のエラー メッセージ

使用している AD DS VM に、Microsoft.Powershell.DSC という名前の拡張機能がすでに関連付けられている場合は、次のようなエラー メッセージが表示されます。

```azure
"error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \\\"DSC Configuration 'AddADDSUser' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: Some error occurred in DSC CreateUser SetScript: \\r\\n\\r\\nException             : Microsoft.ActiveDirectory.Management.ADIdentityNotFoundException: Cannot find an object with \\r\\n                        identity: 'Adam S' under: 'DC=GT090617,DC=onmicrosoft,DC=com'.\\r\\n                           at Microsoft.ActiveDirectory.Management.Commands.ADFactoryUtil.GetObjectFromIdentitySearcher(\\r\\n                        ADObjectSearcher searcher, ADEntity identityObj, String searchRoot, AttributeSetRequest attrs, \\r\\n                        CmdletSessionInfo cmdletSessionInfo, String[]& warningMessages)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.ADFactory`1.GetDirectoryObjectFromIdentity(T \\r\\n                        identityObj, String searchRoot, Boolean showDeleted)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.SetADGroupMember`1.ValidateMembersParameter()\\r\\nTargetObject          : Adam S\\r\\nCategoryInfo          : ObjectNotFound: (Adam S:ADPrincipal) [Add-ADGroupMember], ADIdentityNotFoundException\\r\\nFullyQualifiedErrorId : SetADGroupMember.ValidateMembersParameter,Microsoft.ActiveDirectory.Management.Commands.AddADGro\\r\\n                        upMember\\r\\nErrorDetails          : \\r\\nInvocationInfo        : System.Management.Automation.InvocationInfo\\r\\nScriptStackTrace      : at <ScriptBlock>, C:\\\\Packages\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.83.1.0\\\\DSCWork\\\\DSCADUserCreatio\\r\\n                        nScripts_2020-04-28.2\\\\Script-CreateADDSUser.ps1: line 98\\r\\n                        at <ScriptBlock>, <No file>: line 8\\r\\n                        at ScriptExecutionHelper, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfi\\r\\n                        guration\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 270\\r\\n                        at Set-TargetResource, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfigur\\r\\n                        ation\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 144\\r\\nPipelineIterationInfo : {}\\r\\nPSMessageDetails      : \\r\\n\\r\\n\\r\\n\\r\\n  The SendConfigurationApply function did not succeed.\\\"\\r\\n\\r\\nMore information on troubleshooting is available at https://aka.ms/VMExtensionDSCWindowsTroubleshoot \"\r\n      }\r\n    ]\r\n  }\r\n}"
            }
        ]
    }

```

この問題を解決するには、Microsoft.Powershell.DSC 拡張機能をアンインストールしてから、作業の開始機能をもう一度実行します。

## <a name="error-messages-for-easy-button-prerequisite-job-linked-template"></a>easy-button-prerequisite-job-linked-template のエラー メッセージ

次のようなエラー メッセージが表示された場合、これは easy-button-prerequisite-job-linked-template テンプレートのリソース操作が正常に完了していないことを意味します。

```azure
{
    "status": "Failed",
    "error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\"\r\n  }\r\n}"
            }
        ]
    }
}

```

この問題が発生しているかどうかを確認するには、次の手順を実行します。

1. **easy-button-prerequisite-job-linked-template** を選択してから、ポップアップ表示されたエラー メッセージ ウィンドウで **[OK]** を選択します。

2. **\<prefix\>-deployment リソース グループ** にアクセスし、**resourceSetupRunbook** を選択します。

3. **[失敗]** と表示されている状態を選択します。

4. **[例外]** タブを選択すると、次のようなエラー メッセージが表示されているはずです。
   
   ```azure
   The running command stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Error while creating and adding validation user <your-username-here> to group <your-resource-group-here>
   ```

現在、この問題を根本的に解決する方法はありません。 回避策として、今回は検証ユーザーを作成せずに、Azure Virtual Desktop の作業の開始機能をもう一度実行します。 その後、手動プロセスのみを使用して、新しいユーザーを作成します。

### <a name="validate-that-the-domain-administrator-upn-exists-for-a-new-profile"></a>新しいプロファイルのドメイン管理者 UPN が存在することを検証する

UPN アドレスがテンプレートの問題を引き起こしているかどうかを確認するには、次のようにします。

1. **easy-button-prerequisite-job-linked-template** を選択してから、失敗したステップを選択します。 エラー メッセージを確認します。

2. **\<prefix\>-deployment リソース グループ** に移動し、**resourceSetupRunbook** をクリックします。

3. **[失敗]** と表示されている状態を選択します。

4. **[出力]** タブを選択します。

UPN が新しいサブスクリプションに存在している場合は、次の 2 つの原因が考えられます。

- ユーザーが既に存在するため、作業の開始機能でドメイン管理者プロファイルが作成されませんでした。 これを解決するには、今回は ID プロバイダーにまだ存在しないユーザー名を入力して、作業の開始機能をもう一度実行します。
- 作業の開始機能で、検証ユーザープロファイルが作成されませんでした。 この問題を解決するには、今回は検証ユーザーを作成せずに、作業の開始機能をもう一度実行します。 その後、手動プロセスのみを使用して、新しいユーザーを作成します。

## <a name="error-messages-for-easy-button-inputvalidation-job-linked-template"></a>easy-button-inputvalidation-job-linked-template のエラー メッセージ

easy-button-inputvalidation-job-linked-template テンプレートに問題がある場合は、次のようなエラー メッセージが表示されます。

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'."
    }
}
```

この問題が発生しているかどうかを確認するには、次の手順を実行します。

1. \<prefix\>-Deployment リソース グループを開き、**inputValidationRunbook を探します。**

2. [最近のジョブ] の下に、失敗状態のジョブが表示されます。 **[失敗]** をクリックします。

3. **[ジョブの詳細]** ウィンドウで、 **[例外]** を選択します。

このエラーは、入力した Azure 管理者 UPN が正しくない場合に発生します。 この問題を解決するには、入力したユーザー名とパスワードが正しいことを確認してから、もう一度やり直してください。

## <a name="multiple-vmextensions-per-handler-not-supported"></a>1 つのハンドラーに対して複数の VMExtensions はサポートされていません

Azure AD DS または AD DS を含むサブスクリプションで作業の開始機能を実行すると、この機能によって Microsoft.Powershell.DSC 拡張機能が使用され、検証ユーザーの作成と FSLogix の構成が行われます。 ただし、Azure 内の Windows VM では、複数の同じ種類の拡張機能を同時に実行することはできません。

複数のバージョンの Microsoft.Powershell.DSC を実行しようとすると、次のようなエラー メッセージが表示されます。

```azure
{
    "status": "Failed",
    "error": {
        "code": "BadRequest",
        "message": "Multiple VMExtensions per handler not supported for OS type 'Windows'. VMExtension 'Microsoft.Powershell.DSC' with handler 'Microsoft.Powershell.DSC' already added or specified in input."
    }
}
```

この問題を解決するには、作業の開始機能を実行する前に、ドメイン コントローラー VM から現在実行中のすべての Microsoft.Powershell.DSC インスタンスを削除してください。

## <a name="failure-in-easy-button-prerequisitecompletion-job-linked-template"></a>easy-button-prerequisitecompletion-job-linked-template のエラー

検証ユーザーのユーザー グループは、"USERS" コンテナーにあります。 ただし、正しく機能するためには、ユーザー グループが Azure AD と同期されている必要があります。 されていない場合は、次のようなエラー メッセージが表示されます。

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state ‘Failed’."
    }
}
```

検証ユーザー グループが同期されていないことが原因でこの問題が発生していることを確認するには、\<prefix\>-prerequisites リソース グループを開き、**prerequisiteSetupCompletionRunbook** という名前のファイルを探します。 Runbook を選択し、 **[すべてのログ]** を選択します。

この問題を解決するには、次の手順を実行します。

1. "USERS" コンテナーの Azure AD との同期を有効にします。

2. Azure と同期している組織単位に AVDValidationUsers グループを作成します。

## <a name="next-steps"></a>次のステップ

作業の開始機能については、「[作業の開始機能を使用して Azure Virtual Desktop をデプロイする](getting-started-feature.md)」を参照してください。
