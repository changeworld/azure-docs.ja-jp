---
title: カスタム ゲスト構成ポリシー定義の作成方法
description: ゲスト構成ポリシーの作成方法について説明します。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 1dd1620d0ef41bf28a276cfe2412ca4bdc09d183
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644906"
---
# <a name="how-to-create-custom-guest-configuration-policy-definitions"></a>カスタム ゲスト構成ポリシー定義の作成方法

始める前に、[ゲスト構成](../concepts/guest-configuration.md)の概要を記したページと、ゲスト構成ポリシーの効果について詳しく記されている[ゲスト構成の修復オプションの構成方法](../concepts/guest-configuration-policy-effects.md)に関するページを読むことをお勧めします。

> [!IMPORTANT]
> Azure の仮想マシンには、ゲスト構成拡張機能が必要となります。 すべてのマシンに対して大規模な拡張を展開するために、次のようなポリシー イニシアティブを割り当ててください: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> ゲスト構成パッケージを使って構成を行うには、Azure VM ゲスト構成拡張のバージョン **1.29.24** 以降、または Arc エージェントのバージョン **1.10.0** 以降が必要です。
>
> **AuditIfNotExists** を用いるカスタム ゲスト構成ポリシー定義は一般公開されていますが、ゲスト構成とともに **DeployIfNotExists** を用いる定義は **プレビュー段階** です。

コンプライアンスの監査や Azure または Arc 対応マシンの状態の管理を行う独自のポリシーの作成は、次の手順に従います。

## <a name="install-powershell-7-and-required-powershell-modules"></a>PowerShell 7 と必要な PowerShell モジュールをインストールする

はじめに、ご使用の OS で必要とされるバージョンの PowerShell と `GuestConfiguration` モジュールをインストールするために、[ゲスト構成の作成環境を設定する方法](./guest-configuration-create-setup.md)に関するページのすべての手順に従っていることをご確認ください。

## <a name="create-and-publish-a-guest-configuration-package-artifact"></a>ゲスト構成パッケージの成果物を作成して公開する

まだ行っていない場合は、カスタム ゲスト構成パッケージの成果物を作成して公開するために、[カスタム ゲスト構成パッケージの成果物を作成する方法](./guest-configuration-create.md)に関するページのすべての手順に従ってください。また、ゲスト構成パッケージをご自身の開発環境内で確認するために、[ゲスト構成パッケージの成果物をテストする方法](./guest-configuration-create-test.md)に関するページのすべての手順に従ってください。

## <a name="policy-requirements-for-guest-configuration"></a>ゲスト構成のためのポリシー要件

ゲスト構成の割り当てのプロビジョニングとレポートを自動化するために、ポリシー定義の `metadata` セクションには、ゲスト構成サービスの 2 つのプロパティが含まれている必要があります。 `category` プロパティは「Guest Configuration」に設定する必要があります。また、 `guestConfiguration` という名のセクションにはゲスト構成の割り当てに関する情報を含めなければなりません。 このテキストは、`New-GuestConfigurationPolicy` コマンドレットによって自動的に作成されます。

次の例は `New-GuestConfigurationPolicy` によって自動的に生成される `metadata` セクションを示します。

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

`category` プロパティは「Guest Configuration」 に設定する必要があります。 定義の効果が「DeployIfNotExists」に設定される場合は、`then` セクションにはゲスト構成の割り当てに関するデプロイの詳細が含まれる必要があります。 このテキストは、`New-GuestConfigurationPolicy` コマンドレットによって自動的に作成されます。

### <a name="create-an-azure-policy-definition"></a>Azure Policy の定義を作成する

ゲスト構成のカスタム ポリシー パッケージを作成してアップロードした後、ゲスト構成ポリシー定義を作成します。 `New-GuestConfigurationPolicy` コマンドレットは、カスタム ポリシー パッケージを受け取り、ポリシー定義を作成します。

`New-GuestConfigurationPolicy` の **Policyid** パラメーターには一意の文字列が必要です。 グローバル一意識別子 (GUID) を使うことをお勧めします。 新しく定義する場合には、コマンドレット `New-GUID` を使って新たな GUID を生成してください。 定義を更新する場合は、**PolicyId** に同じ一意の文字列を使用して、正しい定義が確実に更新されるようにしてください。

`New-GuestConfigurationPolicy` コマンドレットのパラメーター:

- **PolicyId**: 定義を識別する GUID またはその他の一意な文字列。
- **ContentUri**: ゲスト構成コンテンツ パッケージの公開 HTTP(S) URI。
- **DisplayName**: ポリシーの表示名。
- **説明**:ポリシーの説明。
- **Parameter**: ハッシュテーブル形式で提供されるポリシー パラメーター。
- **バージョン**:ポリシーのバージョン。
- **パス**:ポリシー定義が作成されるターゲット パス。
- **Platform**: ゲスト構成ポリシーおよびコンテンツ パッケージのためのターゲット プラットフォーム (Windows/Linux)。
- **Mode**: (ApplyAndMonitor、ApplyAndAutoCorrect、Audit) ポリシーが構成を監査すべきか展開すべきかを選択する。 デフォルト値は「Audit」。
- **Tag** は、ポリシー定義に 1 つ以上のタグ フィルターを追加します
- **カテゴリ** は、ポリシー定義のカテゴリ メタデータ フィールドを設定します

「Mode」パラメーターに関してさらに情報が必要な場合は、[ゲスト構成の修復オプションを構成する方法](../concepts/guest-configuration-policy-effects.md)に関するページをご覧ください。

規定されたパスで、カスタム構成パッケージを使って監査を行うポリシー定義を作成してください。

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Verbose
```

規定されたパスで、カスタム構成パッケージを使って構成を展開するポリシー定義を作成してください:

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Mode 'ApplyAndAutoCorrect' `
  -Verbose
```

コマンドレットの出力では、定義の表示名とポリシー ファイルのパスが含まれるオブジェクトが返されます。 監査ポリシー定義を作成する Definition JSON ファイルは **auditIfNotExists.json** という名前であり、構成を適用するためのポリシー定義を作成するファイルは **deployIfNotExists.json** という名前です。

#### <a name="filtering-guest-configuration-policies-using-tags"></a>タグを使用したゲスト構成ポリシーのフィルター処理

Guest Configuration 内のコマンドレットによって作成されたポリシー定義には、必要に応じてタグ用のフィルターを含めることができます。 `New-GuestConfigurationPolicy` の **Tag** パラメーターでは、個々のタグ エントリを含むハッシュテーブルの配列がサポートされています。 タグは、ポリシー定義の `If` セクションに追加されており、ポリシーの割り当てで変更することはできません。

タグをフィルター処理するポリシー定義のスニペットの例を次に示します。

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original guest configuration content
    }
  ]
}
```

#### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>カスタム ゲスト構成ポリシーの定義におけるパラメーターの使用

ゲスト構成では、実行時に構成のプロパティをオーバーライドすることができます。 この機能は、パッケージの MOF ファイル内の値を静的と見なす必要がないことを意味します。 オーバーライドする値は Azure Policy を通じて提供されます。構成の作成方法およびコンパイル方法は変更されません。

`New-GuestConfigurationPolicy` と `Get-GuestConfigurationPacakgeComplianceStatus ` のコマンドレットには、**Parameters** という名前のパラメーターが含まれています。 このパラメーターによって、各パラメーターの詳細をすべて含むハッシュテーブル定義を受け取り、Azure Policy 定義に使用される各ファイルの必要なセクションが作成されます。

次の例では、サービスを監査するためのポリシー定義を作成します。ポリシーの割り当て時に一覧からユーザーが選択します。

```powershell
# This DSC Resource text:
Service 'UserSelectedNameExample'
  {
    Name = 'ParameterValue'
    Ensure = 'Present'
    State = 'Running'
  }`

# Would require the following hashtable:
$PolicyParameterInfo = @(
  @{
    Name = 'ServiceName'                                           # Policy parameter name (mandatory)
    DisplayName = 'windows service name.'                          # Policy parameter display name (mandatory)
    Description = 'Name of the windows service to be audited.'     # Policy parameter description (optional)
    ResourceType = 'Service'                                       # DSC configuration resource type (mandatory)
    ResourceId = 'UserSelectedNameExample'                         # DSC configuration resource id (mandatory)
    ResourcePropertyName = 'Name'                                  # DSC configuration resource property name (mandatory)
    DefaultValue = 'winrm'                                         # Policy parameter default value (optional)
    AllowedValues = @('BDESVC','TermService','wuauserv','winrm')   # Policy parameter allowed values (optional)
  }
)

New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'Audit Windows Service.' `
  -Description 'Audit if a Windows Service isn't enabled on Windows machine.' `
  -Path '.\policies' `
  -Parameter $PolicyParameterInfo `
  -Version 1.0.0
```

### <a name="publish-the-azure-policy-definition"></a>Azure Policy 定義を公開する

最後に、`Publish-GuestConfigurationPolicy` コマンドレットを使用してポリシー定義を発行します。 コマンドレットのパラメーターは、`New-GuestConfigurationPolicy` によって作成される JSON ファイルの場所を指し示す **Path** だけです。

Publish コマンドを実行するには、Azure でポリシー定義を作成できるアクセス権が必要です。 特定の承認要件については、[Azure Policy の概要](../overview.md)に関するページに記載されています。 組み込みロールとしては **リソース ポリシーの共同作成者** をお勧めします。

```powershell
Publish-GuestConfigurationPolicy -Path '.\policies'
```

Azure で作成されるポリシー定義で、定義の割り当てが最後のステップとなります。 [ポータル](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md)、および [Azure PowerShell](../assign-policy-powershell.md) で定義を割り当てる方法を確認してください。

### <a name="optional-piping-output-from-each-command-to-the-next"></a>オプション機能: 各コマンドの出力を次のコマンドに渡す

ゲスト構成におけるコマンドは、名前によるパイプライン パラメーターをサポートします。
`|` 演算子を使って各コマンドの出力を次のコマンドに渡すことができます。
パイプ処理は、各コマンドの出力をコピー/ペーストする必要がないため、高速な反復処理を行う開発環境で役立ちます。

`|` 演算子を使ってシーケンスを実行するには、次の手順を実行します:

```powershell
# End to end flow piping output of each command to the next
$ConfigName         = myConfigName
$ResourceGroupName  = myResourceGroupName
$StorageAccountName = myStorageAccountName
$DisplayName        = 'Configure Linux machine per my scenario.'
$Description        = 'Details about my policy.'
New-GuestConfigurationPackage -Name $ConfigName -Configuration ./$ConfigName.mof -Path ./package/ -Type AuditAndSet -Force |
Publish-GuestConfigurationPackage -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName -Force |
New-GuestConfigurationPolicy -PolicyId 'My GUID' -DisplayName $DisplayName -Description $Description -Path './policies' -Platform 'Linux' -Version 1.0.0 -Mode 'ApplyAndAutoCorrect' |
Publish-GuestConfigurationPolicy
```

## <a name="policy-lifecycle"></a>ポリシーのライフサイクル

ポリシー定義の更新をリリースする場合は、ゲスト構成パッケージと Azure Policy 定義の詳細の両方に対して変更を行います。

> [!NOTE]
> ゲスト構成割り当ての `version` プロパティは Microsoft によって提供されるパッケージに対してのみ効果をもたらします。 カスタム コンテンツのバージョン管理のベスト プラクティスは、ファイル名にバージョンを含めることです。

まず、`New-GuestConfigurationPackage` を実行するときに、以前のバージョンと異なる一意のパッケージの名前を指定します。 名前には、`PackageName_1.0.0` などのバージョン番号を含めることができます。 この例の番号は、パッケージを一意にするためにのみ使用されており、パッケージを他のパッケージよりも新しいまたは古いものとして見なすように指定するものではありません。

次に、以下の各説明に従って、`New-GuestConfigurationPolicy` コマンドレットで使用するパラメーターを更新します。

- **バージョン**:`New-GuestConfigurationPolicy` コマンドレットを実行するときは、現在発行されているバージョンより大きいバージョン番号を指定する必要があります。
- **contentUri**: `New-GuestConfigurationPolicy` コマンドレットを実行するときは、パッケージの場所の URI を指定する必要があります。 ファイル名にパッケージのバージョンを含めると、各リリースでこのプロパティの値が変更されます。
- **contentHash**: このプロパティは、`New-GuestConfigurationPolicy` コマンドレットによって自動的に更新されます。 `New-GuestConfigurationPackage` によって作成されるパッケージのハッシュ値です。 このプロパティは、発行する `.zip` ファイルに対して適切なものである必要があります。 **contentUri** プロパティのみが更新された場合、拡張機能ではコンテンツ パッケージが受け入れられません。

更新されたパッケージをリリースする最も簡単な方法は、この記事で説明されているプロセスを繰り返し、更新されたバージョン番号を指定することです。 このプロセスにより、すべてのプロパティが正しく更新されることが保証されます。

## <a name="next-steps"></a>次のステップ

- Azure portal を使用して[カスタム ポリシー定義を割り当てる](../assign-policy-portal.md)。
- [ゲスト構成ポリシーの割り当てに関するコンプライアンスの詳細](./determine-non-compliance.md#compliance-details-for-guest-configuration)を見る方法を学習する。
