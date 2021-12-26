---
title: Azure Pipelines を使用してカスタム ポリシーをデプロイする
titleSuffix: Azure AD B2C
description: Azure Pipelines を使用して、CI/CD パイプラインに Azure AD B2C カスタム ポリシーをデプロイする方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: f042131bd67c27041ca464fde1be6f4d4915d7a6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039659"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Azure Pipelines を使用してカスタム ポリシーをデプロイする

[Azure Pipelines](/azure/devops/pipelines) では、継続的インテグレーション (CI) と継続的デリバリー (CD) がサポートされており、コードが継続的に一貫してテストおよびビルドされて、ターゲットに出荷されます。 この記事では、Azure Pipelines を使用して、Azure Active Directory B2C (Azure AD B2C) [カスタム ポリシー](user-flow-overview.md)のデプロイ プロセスを自動化する方法について説明します。

> [!IMPORTANT]
> Azure Pipelines を使用した Azure AD B2C カスタム ポリシーの管理では、現在、Microsoft Graph API `/beta` エンドポイントで利用できる **プレビュー** 操作を使用します。 実稼働アプリケーションにおけるこれらの API の使用はサポートされていません。 詳細については、「[Microsoft Graph REST API ベータ版のエンドポイント リファレンス](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)」を参照してください。

## <a name="prerequisites"></a>前提条件

* 「[Active Directory B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md)」にある手順を完了する。
* DevOps 組織を作成していない場合は、[Azure DevOps へのサインアップとサインイン](/azure/devops/user-guide/sign-up-invite-teammates)に関するページの手順に従って新規作成する。  

## <a name="register-an-application-for-management-tasks"></a>管理タスクのためにアプリケーションを登録する

Azure AD B2C ポリシーをデプロイするには、PowerShell スクリプトを使用します。 PowerShell スクリプトが [Microsoft Graph API](microsoft-graph-operations.md) と対話できるようにするには、事前に Azure AD B2C テナントにアプリケーションの登録を作成します。 まだ行っていない場合は、[Microsoft Graph アプリケーションを登録します](microsoft-graph-get-started.md)。

PowerShell スクリプトから MS Graph のデータにアクセスするには、関連する[アプリケーションのアクセス許可](/graph/permissions-reference)を登録されているアプリケーションに付与します。 アプリ登録の **[API のアクセス許可]** 内で **[Microsoft Graph]**  >  **[ポリシー]**  >  **[Policy.ReadWrite.TrustFramework]** アクセス許可を付与します。

## <a name="configure-an-azure-repo"></a>Azure リポジトリを構成する

Microsoft Graph アプリケーションを登録すると、ポリシー ファイル用のリポジトリを構成する準備が整います。

1. [Azure DevOps 組織](https://azure.microsoft.com/services/devops/)にサインインします。
1. [新しいプロジェクトを作成する][devops-create-project]か、既存のプロジェクトを選択します。
1. プロジェクト内で **[リポジトリ]** に移動し、 **[ファイル]** を選択します。 
1. 既存のレポジトリを選択するか、新規に作成します。
1. リポジトリのルート ディレクトリに、`B2CAssets` という名前のフォルダーを作成します。 Azure AD B2C カスタム ポリシー ファイルを *B2CAssets* フォルダーに追加します。
1. リポジトリのルート　ディレクトリに、`Scripts`　という名前のフォルダーを作成します。 PowerShell ファイル *DeployToB2C.ps1* を作成します。 次の PowerShell スクリプトを *DeployToB2C.ps1* に貼り付けます。 
1. 変更を **コミット** して **プッシュ** します。

次のスクリプトは、Azure AD からアクセス トークンを取得します。 このトークンを使用して、スクリプトは MS Graph API を呼び出して、*B2CAssets* フォルダーにポリシーをアップロードします。 ポリシーをアップロードする前に、その内容を変更することもできます。 たとえば、`tenant-name.onmicrosoft.com` を実際のテナント名に置き換えます。

```PowerShell
[Cmdletbinding()]
Param(
    [Parameter(Mandatory = $true)][string]$ClientID,
    [Parameter(Mandatory = $true)][string]$ClientSecret,
    [Parameter(Mandatory = $true)][string]$TenantId,
    [Parameter(Mandatory = $true)][string]$Folder,
    [Parameter(Mandatory = $true)][string]$Files
)

try {
    $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

    $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
    $token = $response.access_token

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Content-Type", 'application/xml')
    $headers.Add("Authorization", 'Bearer ' + $token)

    # Get the list of files to upload
    $filesArray = $Files.Split(",")

    Foreach ($file in $filesArray) {

        $filePath = $Folder + $file.Trim()

        # Check if file exists
        $FileExists = Test-Path -Path $filePath -PathType Leaf

        if ($FileExists) {
            $policycontent = Get-Content $filePath

            # Optional: Change the content of the policy. For example, replace the tenant-name with your tenant name.
            # $policycontent = $policycontent.Replace("your-tenant.onmicrosoft.com", "contoso.onmicrosoft.com")     
    
    
            # Get the policy name from the XML document
            $match = Select-String -InputObject $policycontent  -Pattern '(?<=\bPolicyId=")[^"]*'
    
            If ($match.matches.groups.count -ge 1) {
                $PolicyId = $match.matches.groups[0].value
    
                Write-Host "Uploading the" $PolicyId "policy..."
    
                $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
                $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers
    
                Write-Host "Policy" $PolicyId "uploaded successfully."
            }
        }
        else {
            $warning = "File " + $filePath + " couldn't be not found."
            Write-Warning -Message $warning
        }
    }
}
catch {
    Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

    $_

    $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
    $streamReader.BaseStream.Position = 0
    $streamReader.DiscardBufferedData()
    $errResp = $streamReader.ReadToEnd()
    $streamReader.Close()

    $ErrResp

    exit 1
}

exit 0
```

## <a name="configure-azure-pipelines"></a>Azure Pipelines を構成する

リポジトリが初期化され、カスタム ポリシー ファイルが設定されたら、リリース パイプラインをセットアップする準備が整います。 パイプラインを作成するには、次の手順に従います。

1. プロジェクトで、 **[パイプライン]**  >  **[リリース]**  >  **[新しいパイプライン]** の順に選択します。
1. **[テンプレートの選択]** で、 **[空のジョブ]**  を選択し、 **[適用]** を選択します。
1. **ステージ名** (たとえば、*DeployCustomPolicies*) を入力し、ペインを閉じます。
1. **[Add an artifact]\(成果物の追加\)** を選択し、 **[ソースの種類]** で **[Azure リポジトリ]** を選択します。
    1. **[プロジェクト]** で、自分のプロジェクトを選択します。
    1. *Scripts* フォルダーを含む **ソース (リポジトリ)** を選択します。
    1. **規定のブランチ** (たとえば、*master*) を選択します。
    1. **[既定のバージョン]** の設定は、 *[既定のブランチからの最新バージョン]* のままにします。
    1. リポジトリの **ソース エイリアス** を入力します。 たとえば、"*policyRepo*" などです。 
1. **[追加]** を選択します。
1. 目的に合わせてパイプラインの名前を変更します。 たとえば、"*Deploy Custom Policy Pipeline*" などです。
1. **[保存]** を選択して、パイプライン構成を保存します。

### <a name="configure-pipeline-variables"></a>パイプライン変数を構成する

パイプライン変数を使用すると、パイプラインのさまざまな部分に重要なデータを簡単に取り込むことができます。 以下の変数は、Azure AD B2C 環境に関する情報を提供します。

| Name | 値 |
| ---- | ----- |
| `clientId` | 前に登録したアプリケーションの **アプリケーション (クライアント) ID**。 |
| `clientSecret` | 前に作成した **クライアント シークレット** の値。 <br /> 変数の型を **シークレット** に変更します (ロック アイコンを選択)。 |
| `tenantId` | `your-b2c-tenant.onmicrosoft.com`。ここで、*your-b2c-tenant* は Azure AD B2C テナントの名前です。 |

パイプライン変数を追加するには、次の手順に従います。

1. パイプラインで、 **[変数]** タブを選択します。
1. **[パイプライン変数]** で、値を指定して上記の変数を追加します。
1. **[保存]** を選択して変数を保存します。

### <a name="add-pipeline-tasks"></a>パイプライン タスクを追加する

パイプライン タスクは、アクションを実行する事前パッケージされたスクリプトです。 *DeployToB2C.ps1* PowerShell スクリプトを呼び出すタスクを追加します。

1. 作成したパイプラインで、 **[タスク]** タブを選択します。
1. **[エージェント ジョブ]** を選択し、プラス記号 ( **+** ) を選択してエージェント ジョブにタスクを追加します。
1. 「**PowerShell**」を検索して選択します。 "Azure PowerShell"、"ターゲット マシンでの PowerShell"、または別の PowerShell エントリを選択しないでください。
1. 新しく追加された **PowerShell スクリプト** タスクを選択します。
1. PowerShell Script タスクに対して次の値を入力します。
    * **タスクのバージョン**: 2.*
    * **表示名**:このタスクでアップロードするポリシーの名前。 たとえば、*B2C_1A_TrustFrameworkBase*。
    * **[種類]** :ファイル パス
    * **スクリプトのパス**:省略記号 (**_..._* _) を選択し、_Scripts* フォルダーに移動して、*DeployToB2C.ps1* ファイルを選択します。
    * **引数**: 次の PowerShell スクリプトを入力します。 


        ```PowerShell
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -Folder $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/ -Files "TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml,ProfileEdit.xml,PasswordReset.xml"
        ```
        
        `-Files` パラメーターは、デプロイするポリシー ファイルのカンマ区切りリストです。 ポリシー ファイルを使用してリストを更新します。
        
        > [!IMPORTANT]
        >  ポリシーが正しい順序でアップロードされることを確認します。 まず、基本ポリシー、拡張機能ポリシー、証明書利用者ポリシーの順です。 (例: `TrustFrameworkBase.xml,TrustFrameworkLocalization.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`)。
        
1. **[保存]** を選択して、エージェント ジョブを保存します。

## <a name="test-your-pipeline"></a>パイプラインをテストする

リリース パイプラインをテストするには、次のようにします。

1. **[パイプライン]** 、 **[リリース]** の順に選択します。
1. 前に作成したパイプラインを選択します。たとえば、*DeployCustomPolicies* などです。
1. **[リリースの作成]** を選択し、次に **[作成]** を選択してリリースをキューに入れます。

リリースがキューに入れられたことを示す通知バナーが表示されます。 その状態を表示するには、通知バナー内のリンクを選択するか、 **[リリース]** タブの一覧から選択します。


## <a name="next-steps"></a>次のステップ

各項目の詳細情報

* [クライアント資格情報を使用したサービス間の呼び出し](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
