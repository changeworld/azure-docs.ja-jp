---
title: Windows 用のゲスト構成ポリシーを作成する方法
description: Windows に対する Azure Policy のゲスト構成ポリシーを作成する方法について説明します。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: a75525b25945dd9548d7c293d5965cc67eb463dc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509620"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Windows 用のゲスト構成ポリシーを作成する方法

カスタム ポリシーを作成する前に、[Azure Policy のゲスト構成](../concepts/guest-configuration.md)のページで、概念上の概要情報を読むことをお勧めします。
 
Linux のゲスト構成ポリシーを作成する方法の詳細については、[Linux 用のゲスト構成ポリシーを作成する方法](./guest-configuration-create-linux.md)に関するページを参照してください

Windows の監査時に、ゲスト構成では [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) リソース モジュールを使用して構成ファイルが作成されます。 DSC 構成では、マシンが満たす必要のある条件を定義します。
構成の評価が失敗した場合、ポリシー効果の **auditIfNotExists** がトリガーされて、マシンは**非準拠**と見なされます。

[Azure Policy のゲスト構成](../concepts/guest-configuration.md)は、マシン内の設定を監査するためにのみ使用できます。 マシン内の設定の修復はまだ利用できません。

Azure または非 Azure マシンの状態を検証するための独自の構成を作成するには、次のアクションを使用します。

> [!IMPORTANT]
> ゲスト構成でのカスタム ポリシーは、プレビュー機能です。
>
> Azure の仮想マシンで監査を実行するには、ゲスト構成拡張機能が必要です。
> すべての Windows マシンに拡張機能を大規模にデプロイするには、次のポリシー定義を割り当てます。
>   - [Windows VM でゲスト構成ポリシーを有効にするための前提条件をデプロイする。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>PowerShell モジュールをインストールする

ゲスト構成アーティファクトの作成、アーティファクトの自動テスト、ポリシー定義の作成、およびポリシーの発行は、PowerShell のゲスト構成モジュールを使用して完全に自動化できます。 モジュールは、ローカルで実行している PowerShell 6.2 以降、[Azure Cloud Shell](https://shell.azure.com)、または [Azure PowerShell Core Docker イメージ](https://hub.docker.com/r/azuresdk/azure-powershell-core)を使用して、Windows、macOS、または Linux を実行しているマシンにインストールできます。

> [!NOTE]
> Linux では、構成のコンパイルはまだサポートされていません。

### <a name="base-requirements"></a>基本要件

モジュールをインストールできるオペレーティング システム:

- Linux
- macOS
- Windows

ゲスト構成のリソース モジュールには、次のソフトウェアが必要です。

- PowerShell 6.2 以降。 インストールされていない場合は、こちらの[手順](/powershell/scripting/install/installing-powershell)に従ってください。
- Azure PowerShell 1.5.0 以降。 インストールされていない場合は、こちらの[手順](/powershell/azure/install-az-ps)に従ってください。
  - AZ モジュール 'Az.Accounts' および 'Az.Resources' のみが必要です。

### <a name="install-the-module"></a>モジュールのインストール

PowerShell で **GuestConfiguration** モジュールをインストールするには:

1. PowerShell プロンプトから、次のコマンドを実行します。

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. モジュールがインポートされていることを確認します。

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows 用のゲスト構成アーティファクトとポリシー

ゲスト構成では、Windows で監査対象を記述するための言語の抽象化として PowerShell Desired State Configuration が使用されます。 エージェントは PowerShell 6.2 のスタンドアロン インスタンスを読み込みます。そのため、Windows PowerShell 5.1 での PowerShell DSC の使用と競合せず、PowerShell 6.2 以降を事前にインストールする必要はありません。

DSC の概念と用語の概要については、[PowerShell DSC の概要](/powershell/scripting/dsc/overview/overview)に関する記事をご覧ください。

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>ゲスト構成モジュールと Windows PowerShell DSC モジュールの違い

ゲスト構成によってコンピューターを監査する場合:

1. エージェントでは最初に `Test-TargetResource` を実行して、構成が正しい状態であるかどうかを判定します。
1. 関数によって返されるブール値は、ゲスト割り当ての Azure Resource Manager ステータスが準拠しているべきか否かを決定します。
1. プロバイダーによって `Get-TargetResource` が実行され、各設定の現在の状態が返されます。これにより、コンピューターが準拠していない理由と、現在の状態が準拠していることの確認に関する両方の詳細情報が得られます。

### <a name="get-targetresource-requirements"></a>Get-TargetResource の要件

関数 `Get-TargetResource` には、Windows Desired State Configuration では不要なゲスト構成の特殊な要件があります。

- 返されるハッシュテーブルには **Reasons** という名前のプロパティが含まれている必要があります。
- Reason プロパティは配列である必要があります。
- 配列内の各項目は、**コード**および**フレーズ**という名前のキーを持つハッシュテーブルである必要があります。

Reason プロパティは、マシンが準拠していない場合に情報をどのように表示するかをサービスが標準化するために使用されます。 Reasons の各項目は、そのリソースが準拠していない「理由」であると考えることができます。 リソースが複数の理由で準拠していない可能性があるため、プロパティは配列となっています。

サービスでは、プロパティ **コード**および**フレーズ**を指定する必要があります。 カスタムリソースを作成する場合、リソースが準拠していない理由として表示するテキスト (通常は stdout) を**フレーズ**の値に設定します。 **コード**には特定の書式設定要件があるため、監査を行うために使用されたリソースに関する情報をレポートではっきり表示できます。 このソリューションにより、ゲスト構成を拡張できます。 出力を、**Phrase** プロパティの文字列値として返すことができる限り、任意のコマンドを実行できます。

- **コード** (文字列):リソースの名前、繰り返し、およびスペースを含まない短い名前を理由の識別子として指定します。 これら 3 つの値は、スペースを含まず、コロンで区切られている必要があります。
  - たとえば、`registry:registry:keynotpresent` などです
- **フレーズ** (文字列):設定が準拠していない理由を説明するための、人間が判読できるテキスト。
  - たとえば、`The registry key $key is not present on the machine.` などです

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

また、Reason プロパティは、リソースのスキーマ MOF に埋め込みクラスとして追加する必要があります。

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>構成要件

カスタム構成の名前は、すべての場所で一貫している必要があります。 コンテンツ パッケージの .zip ファイルの名前、MOF ファイル内の構成名、および Resource Manager テンプレート内のゲスト割り当て名は同じである必要があります。

### <a name="scaffolding-a-guest-configuration-project"></a>ゲスト構成プロジェクトのスキャフォールディング

サンプル コードを使用して作業開始プロセスの迅速化に取り組む開発者は、**ゲスト構成プロジェクト**という名前のコミュニティ プロジェクトをインストールできます。 プロジェクトによって、[Plaster](https://github.com/powershell/plaster) PowerShell モジュールのテンプレートがインストールされます。 このツールを使用することで、作業中の構成とサンプルリソースを含むプロジェクトと、プロジェクトを検証するための一連の [Pester](https://github.com/pester/pester) をスキャフォールディングできます。 このテンプレートには、ゲスト構成パッケージの構築と検証を自動化するための Visual Studio Code のタスク ランナーも含まれています。 詳細については、[ゲスト構成プロジェクト](https://github.com/microsoft/guestconfigurationproject) に関する GitHub プロジェクトを参照してください。

一般的な構成の操作に関する詳細については、[構成の作成、コンパイル、適用](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)に関する記事をご覧ください。

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>ゲスト構成アーティファクトの予想されるコンテンツ

完成したパッケージは、Azure Policy の定義を作成するためにゲスト構成によって使われます。 パッケージは次のもので構成されます。

- MOF としてのコンパイル済み DSC 構成
- モジュール フォルダー
  - GuestConfiguration モジュール
  - DscNativeResources モジュール
  - (Windows) MOF に必要な DSC リソース モジュール

PowerShell コマンドレットは、パッケージの作成に役立ちます。
ルート レベル フォルダーまたはバージョン フォルダーは必要ありません。
パッケージ形式は .zip ファイルである必要があります。

### <a name="storing-guest-configuration-artifacts"></a>ゲスト構成成果物の保存

.zip パッケージは、管理対象の仮想マシンからアクセスできる場所に保存されている必要があります。
たとえば、GitHub リポジトリ、Azure リポジトリ、Azure Storage などです。 パッケージを公開したくない場合は、URL に [SAS トークン](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)を含めることができます。
また、マシンの[サービス エンドポイント](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)をプライベート ネットワークに実装することもできますが、この構成はパッケージへのアクセスにのみ適用され、サービスとの通信には適用されません。

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Windows 用のカスタム ゲスト構成監査ポリシーを作成する手順

DSC 構成を作成して設定を監査します。 次の PowerShell スクリプトの例では、**AuditBitLocker** という名前の構成を作成し、**PsDscResources** リソース モジュールをインポートし、`Service` リソースを使って実行中のサービスを監査しています。 構成スクリプトは、Windows または macOS コンピューターから実行できます。

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

このファイルを `config.ps1` という名前でプロジェクト フォルダーに保存します。 ターミナルで `./config.ps1` を実行して、これを PowerShell で実行します。 新しい mof ファイルが作成されます。

`Node AuditBitlocker` コマンドは技術的には必須ではありませんが、既定の `localhost.mof` ではなく `AuditBitlocker.mof` という名前のファイルを生成します。 .mof ファイル名を構成に従わせることにより、大規模な運用時に多くのファイルを簡単に整理できます。

MOF をコンパイルしたら、サポート ファイルをまとめてパッケージ化する必要があります。 完成したパッケージは、Azure Policy の定義を作成するためにゲスト構成によって使われます。

`New-GuestConfigurationPackage` コマンドレットでパッケージを作成します。 構成に必要なモジュールは、`$Env:PSModulePath` 内で利用可能になっている必要があります。 Windows コンテンツを作成するときの `New-GuestConfigurationPackage` コマンドレットのパラメーター:

- **Name**:ゲスト構成のパッケージ名。
- **構成**:コンパイル済み DSC 構成ドキュメントの完全なパス。
- **パス**:出力フォルダーのパス。 このパラメーターは省略可能です。 指定しないと、パッケージは現在のディレクトリに作成されます。

次のコマンドを実行して、前の手順で指定した構成を使用してパッケージを作成します。

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

構成パッケージを作成したら、Azure に発行する前に、ワークステーションまたは CI/CD 環境からパッケージをテストできます。 GuestConfiguration コマンドレット `Test-GuestConfigurationPackage` には、Azure マシンで使われるのと同じエージェントが開発環境に含まれます。 このソリューションを使って、有料のクラウド環境にリリースする前に、ローカル環境で統合テストを実行できます。

エージェントは実際にローカル環境を評価しているため、ほとんどの場合、監査を計画しているのと同じ OS プラットフォームで Test- コマンドレットを実行する必要があります。 テストでは、コンテンツ パッケージに含まれているモジュールのみが使用されます。

`Test-GuestConfigurationPackage` コマンドレットのパラメーター:

- **Name**:ゲスト構成ポリシーの名前。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **パス**:ゲスト構成パッケージの完全なパス。

次のコマンドを実行して、前の手順で作成したパッケージをテストします。

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

コマンドレットでは、PowerShell パイプラインからの入力もサポートされています。 `New-GuestConfigurationPackage` コマンドレットの出力を `Test-GuestConfigurationPackage` コマンドレットにパイプします。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

次の手順では、ファイルを BLOB ストレージに発行します。 次のスクリプトには、このタスクを自動化するために使用できる関数が含まれています。 `publish` 関数で使用されるコマンドには、`Az.Storage` モジュールが必要です。

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

ゲスト構成のカスタム ポリシー パッケージを作成してアップロードした後、ゲスト構成ポリシー定義を作成します。 `New-GuestConfigurationPolicy` コマンドレットは、カスタム ポリシー パッケージを受け取り、ポリシー定義を作成します。

`New-GuestConfigurationPolicy` コマンドレットのパラメーター:

- **ContentUri**: ゲスト構成コンテンツ パッケージのパブリック HTTP(S) URI。
- **DisplayName**: ポリシーの表示名。
- **説明**:ポリシーの説明。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **バージョン**:ポリシーのバージョン。
- **パス**:ポリシー定義が作成されるターゲット パス。
- **Platform**: ゲスト構成ポリシーとコンテンツ パッケージのターゲット プラットフォーム (Windows/Linux)。

次の例では、カスタム ポリシー パッケージから指定されたパスにポリシー定義を作成します。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy` により、次のファイルが作成されます。

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

コマンドレットの出力では、イニシアティブの表示名とポリシー ファイルのパスが含まれるオブジェクトが返されます。

最後に、`Publish-GuestConfigurationPolicy` コマンドレットを使用してポリシー定義を発行します。 コマンドレットのパラメーターは、`New-GuestConfigurationPolicy` によって作成される JSON ファイルの場所を指し示す **Path** だけです。

Publish コマンドを実行するには、Azure でポリシーを作成するためのアクセス権が必要です。 特定の承認要件については、[Azure Policy の概要](../overview.md)に関するページに記載されています。 最適な組み込みロールは、**リソース ポリシーの共同作成者**です。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy` コマンドレットは、PowerShell パイプラインからパスを受け取ります。 この機能では、パイプされたコマンドの 1 つのセットで、ポリシー ファイルを作成して発行できます。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Azure で作成されるポリシーに関する最後のステップでは、イニシアティブを割り当てます。 [ポータル](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md)、および [Azure PowerShell](../assign-policy-powershell.md) でイニシアティブを割り当てる方法について確認できます。

> [!IMPORTANT]
> ゲスト構成ポリシーは**常に**、"_AuditIfNotExists_" ポリシーと "_DeployIfNotExists_" ポリシーを組み合わせたイニシアティブを使って割り当てる必要があります。 "_AuditIfNotExists_" ポリシーのみを割り当てた場合は、前提条件がデプロイされず、ポリシーでは、準拠しているサーバーが常に "0" と示されます。

_DeployIfNotExists_ 効果でポリシー定義を割り当てるには、追加のレベルのアクセス権が必要です。 最小限の権限を付与するために、**リソース ポリシーの共同作成者**を拡張するカスタム ロール定義を作成できます。 次の例では、追加のアクセス許可 _Microsoft.Authorization/roleAssignments/write_ を持つ **リソース ポリシーの共同作成者 DINE** という名前のロールを作成します。

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>カスタム ゲスト構成ポリシーでのパラメーターの使用

ゲスト構成では、実行時に構成のプロパティをオーバーライドすることができます。 この機能は、パッケージの MOF ファイル内の値を静的と見なす必要がないことを意味します。 オーバーライドする値は Azure Policy を通じて提供され、構成の作成方法またはコンパイル方法には影響しません。

`New-GuestConfigurationPolicy` と `Test-GuestConfigurationPolicyPackage` のコマンドレットには、**Parameters** という名前のパラメーターが含まれています。 このパラメーターによって、各パラメーターの詳細をすべて含むハッシュテーブル定義を受け取り、Azure Policy 定義に使用される各ファイルの必要なセクションが作成されます。

次の例では、サービスを監査するためのポリシー定義を作成します。ポリシーの割り当て時に一覧からユーザーが選択します。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>ポリシーのライフサイクル

ポリシーの更新をリリースする場合、注意が必要な 2 つのフィールドがあります。

- **バージョン**:`New-GuestConfigurationPolicy` コマンドレットを実行するときは、現在発行されているバージョンより大きいバージョン番号を指定する必要があります。 プロパティによって、更新されたパッケージをエージェントが認識できるように、ゲスト構成割り当てのバージョンが更新されます。
- **contentHash**: このプロパティは、`New-GuestConfigurationPolicy` コマンドレットによって自動的に更新されます。 `New-GuestConfigurationPackage` によって作成されるパッケージのハッシュ値です。 このプロパティは、発行する `.zip` ファイルに対して適切なものである必要があります。 **contentUri** プロパティのみが更新された場合、拡張機能ではコンテンツ パッケージが受け入れられません。

更新されたパッケージをリリースする最も簡単な方法は、この記事で説明されているプロセスを繰り返し、更新されたバージョン番号を指定することです。 このプロセスにより、すべてのプロパティが正しく更新されることが保証されます。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows グループ ポリシー コンテンツから Azure Policy ゲスト構成への変換

Windows マシンを監査する場合のゲスト構成は、PowerShell Desired State Configuration 構文の実装です。 DSC コミュニティでは、エクスポートしたグループ ポリシー テンプレートを DSC 形式に変換するためのツールが公開されています。 このツールを前述のゲスト構成コマンドレットと共に使用することで、Windows グループ ポリシーのコンテンツを変換し、Azure Policy 用にパッケージ化および公開して監査することができます。 ツールの使用の詳細については、[クイックスタート: グループ ポリシーを DSC に変換する](/powershell/scripting/dsc/quickstarts/gpo-quickstart)」という記事を参照してください。
コンテンツの変換後は、パッケージを作成して Azure Policy として公開する上記の手順は、他の DSC コンテンツと同じです。

## <a name="optional-signing-guest-configuration-packages"></a>省略可能:ゲスト構成パッケージに署名する

ゲスト構成カスタム ポリシーでは、SHA256 ハッシュを使用して、ポリシー パッケージが変更されていないことが検証されます。
必要に応じて、お客様は証明書を使ってパッケージに署名し、署名されたコンテンツのみを許可するようにゲスト構成拡張機能を強制することもできます。

このシナリオを有効にするには、2 つのステップを実行する必要があります。 コマンドレットを実行してコンテンツ パッケージに署名し、コードが署名されていることを要求する必要があるようにマシンにタグを追加します。

署名検証機能を使用するには、`Protect-GuestConfigurationPackage` コマンドレットを実行して、発行前にパッケージに署名します。 このコマンドレットには "コード署名" 証明書が必要です。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` コマンドレットのパラメーター:

- **パス**:ゲスト構成パッケージの完全なパス。
- **Certificate**: パッケージに署名するためのコード署名証明書。 このパラメーターは、Windows 用のコンテンツに署名する場合にのみサポートされます。

GuestConfiguration エージェントにより、Windows マシンの場合は "信頼されたルート証明機関" に、Linux マシンの場合はパス `/usr/local/share/ca-certificates/extra` に、証明書の公開キーが存在していることが求められます。 署名されたコンテンツをノードで検証するには、カスタム ポリシーを適用する前に、マシンに証明書の公開キーをインストールします。 このプロセスは、VM 内で任意の方法を使うか、Azure Policy を使って、行うことができます。 テンプレートの例は、[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)で提供されています。
Key Vault のアクセス ポリシーでは、デプロイ中にコンピューティング リソース プロバイダーが証明書にアクセスできるようにする必要があります。 詳しい手順については、[Azure Resource Manager の仮想マシンの Key Vault を設定する](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)に関する記事をご覧ください。

署名証明書から公開キーをエクスポートしてマシンにインポートする例を次に示します。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

コンテンツを発行した後、コード署名が必要なすべての仮想マシンに、名前が `GuestConfigPolicyCertificateValidation` で値が `enabled` のタグを追加します。 Azure Policy を使用して大規模にタグを配信する方法については、[タグのサンプル](../samples/built-in-policies.md#tags)に関する記事を参照してください。 このタグを配置すると、`New-GuestConfigurationPolicy` コマンドレットを使って生成されるポリシー定義では、ゲスト構成拡張による要件が有効になります。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>ゲスト構成ポリシー割り当てのトラブルシューティング (プレビュー)

Azure Policy ゲスト構成割り当てのトラブルシューティングに役立つツールをプレビューで利用できます。 このツールはプレビュー段階であり、[Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/) というモジュール名で PowerShell ギャラリーに公開されています。

このツールのコマンドレットの詳細については、PowerShell の Get-Help コマンドを使用して、組み込みのガイダンスを参照してください。 ツールは頻繁に更新されるため、この方法が最新の情報を取得するための最適な方法です。

## <a name="next-steps"></a>次のステップ

- [ゲスト構成](../concepts/guest-configuration.md)による VM の監査について学習します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](get-compliance-data.md)方法を学習します。
