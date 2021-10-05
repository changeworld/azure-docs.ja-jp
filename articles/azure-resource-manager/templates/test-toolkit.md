---
title: ARM テンプレート テスト ツールキット
description: テンプレートで Azure Resource Manager テンプレート (ARM テンプレート) テスト ツールキットを実行する方法について説明します。 ツールキットを使用すると、推奨される方法を実装しているかどうかを確認できます。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c02d7183304ec3474427bf1687d67d7dd7058d44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576658"
---
# <a name="use-arm-template-test-toolkit"></a>ARM テンプレート テスト ツールキットを使用する

[Azure Resource Manager テンプレート (ARM テンプレート) テスト ツールキット](https://aka.ms/arm-ttk)では、推奨される方法がテンプレートで使用されているかどうかが確認されます。 テンプレートが推奨されるプラクティスに準拠していない場合は、推奨される変更を含む警告の一覧が返されます。 テスト ツールキットを使用すると、テンプレートの開発での一般的な問題を回避する方法がわかります。 この記事では、テスト ツールキットを実行する方法と、テストを追加または削除する方法について説明します。 テストを実行する方法、または特定のテストを実行する方法の詳細については、「[テスト パラメーター](#test-parameters)」を参照してください。

ツールキットは、PowerShell または CLI のコマンドから実行できる一連の PowerShell スクリプトです。 これらのテストは推奨されるものですが、必須ではありません。 自分の目標に関連するテストを決定し、実行するテストをカスタマイズできます。

このツールキットには、次の 4 つのテスト セットが含まれています。

- [ARM テンプレートのテスト ケース](template-test-cases.md)
- [パラメーター ファイルのテスト ケース](parameter-file-test-cases.md)
- [createUiDefinition.json のテスト ケース](createUiDefinition-test-cases.md)
- [すべてのファイルのテスト ケース](all-files-test-cases.md)

### <a name="microsoft-learn"></a>Microsoft Learn

ARM テンプレート テスト ツールキットの詳細と、ハンズオン ガイダンスについては、**Microsoft Learn** の「[ARM テンプレート テスト ツールキットを使用して Azure リソースを検証する](/learn/modules/arm-template-test)」を参照してください。

## <a name="install-on-windows"></a>Windows にインストールする

1. PowerShell をまだ持っていない場合は、[Windows に PowerShell をインストール](/powershell/scripting/install/installing-powershell-core-on-windows)します。

1. テスト ツールキットの[最新の .zip ファイル](https://aka.ms/arm-ttk-latest)をダウンロードして展開します。

1. PowerShell を開始します。

1. テスト ツールキットを抽出したフォルダーに移動します。 そのフォルダー内で _arm-ttk_ フォルダーに移動します。

1. [実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)によってインターネットからスクリプトがブロックされる場合は、スクリプト ファイルのブロックを解除する必要があります。 _arm-ttk_ フォルダーにいることを確認します。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. モジュールをインポートします。

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. テストを実行するには、次のコマンドを使用します。

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Linux にインストールする

1. PowerShell をまだ持っていない場合は、[Linux に PowerShell をインストール](/powershell/scripting/install/installing-powershell-core-on-linux)します。

1. テスト ツールキットの[最新の .zip ファイル](https://aka.ms/arm-ttk-latest)をダウンロードして展開します。

1. PowerShell を開始します。

   ```bash
   pwsh
   ```

1. テスト ツールキットを抽出したフォルダーに移動します。 そのフォルダー内で _arm-ttk_ フォルダーに移動します。

1. [実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)によってインターネットからスクリプトがブロックされる場合は、スクリプト ファイルのブロックを解除する必要があります。 _arm-ttk_ フォルダーにいることを確認します。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. モジュールをインポートします。

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. テストを実行するには、次のコマンドを使用します。

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>macOS へのインストール

1. PowerShell をまだ持っていない場合は、[macOS に PowerShell をインストール](/powershell/scripting/install/installing-powershell-core-on-macos)します。

1. `coreutils` をインストールします。

   ```bash
   brew install coreutils
   ```

1. テスト ツールキットの[最新の .zip ファイル](https://aka.ms/arm-ttk-latest)をダウンロードして展開します。

1. PowerShell を開始します。

   ```bash
   pwsh
   ```

1. テスト ツールキットを抽出したフォルダーに移動します。 そのフォルダー内で _arm-ttk_ フォルダーに移動します。

1. [実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)によってインターネットからスクリプトがブロックされる場合は、スクリプト ファイルのブロックを解除する必要があります。 _arm-ttk_ フォルダーにいることを確認します。

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. モジュールをインポートします。

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. テストを実行するには、次のコマンドを使用します。

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>結果の形式

合格になったテストは **緑色** で表示され、前に `[+]` が付きます。

不合格になったテストは **赤色** で表示され、前に `[-]` が付きます。

警告が付いたテストは **黄色** で表示され、前に `[?]` が付きます。

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="テスト結果を表示する。":::

テキストの結果は次のようになります。

```powershell
deploymentTemplate
[+] adminUsername Should Not Be A Literal (6 ms)
[+] apiVersions Should Be Recent In Reference Functions (9 ms)
[-] apiVersions Should Be Recent (6 ms)
    Api versions must be the latest or under 2 years old (730 days) - API version 2019-06-01 of
    Microsoft.Storage/storageAccounts is 760 days old
    Valid Api Versions:
    2021-04-01
    2021-02-01
    2021-01-01
    2020-08-01-preview

[+] artifacts parameter (4 ms)
[+] CommandToExecute Must Use ProtectedSettings For Secrets (9 ms)
[+] DependsOn Best Practices (5 ms)
[+] Deployment Resources Must Not Be Debug (6 ms)
[+] DeploymentTemplate Must Not Contain Hardcoded Uri (4 ms)
[?] DeploymentTemplate Schema Is Correct (6 ms)
    Template is using schema version '2015-01-01' which has been deprecated and is no longer
    maintained.
```

## <a name="test-parameters"></a>テスト パラメーター

`-TemplatePath` パラメーターを指定した場合、このツールキットは、そのフォルダーに _azuredeploy.json_ または _maintemplate.json_ という名前のテンプレートがないかどうか調べます。 最初にこのテンプレートがテストされた後、そのフォルダーとサブフォルダー内の他のすべてのテンプレートがテストされます。 他のテンプレートは、リンクされたテンプレートとしてテストされます。 パスに [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md) という名前のファイルが含まれている場合は、UI 定義に関連するテストが実行されます。 そのフォルダー内のパラメーター ファイルとすべての JSON ファイルに対してもテストが実行されます。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

そのフォルダー内の 1 つのファイルをテストするには、`-File` パラメーターを追加します。 ただし、そのフォルダーには引き続き _azuredeploy.json_ または _maintemplate.json_ という名前のメイン テンプレートが存在する必要があります。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

既定では、すべてのテストが実行されます。 実行する個々のテストを指定するには、`-Test` パラメーターを使用し、テスト名を指定します。 テスト名については、[ARM テンプレート](template-test-cases.md)、[パラメーター ファイル](parameter-file-test-cases.md)、[createUiDefinition.json](createUiDefinition-test-cases.md)、[すべてのファイル](all-files-test-cases.md)の各テスト ケースに関するページを参照してください。

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>テストをカスタマイズする

既定のテストをカスタマイズするか、または独自のテストを作成できます。 テストを完全に削除するには、そのフォルダーから _.test.ps1_ ファイルを削除します。

このツールキットには、特定のファイルの種類に対して実行される既定のテストを含む、次の 4 つのフォルダーが含まれています。

- ARM テンプレート: _\arm-ttk\testcases\deploymentTemplate_
- パラメーター ファイル: _\arm-ttk\testcases\deploymentParameters_
- [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md): _\arm-ttk\testcases\CreateUIDefinition_
- すべてのファイル: _\arm-ttk\testcases\AllFiles_

### <a name="add-a-custom-test"></a>カスタム テストを追加する

独自のテストを追加するには、次の名前付け規則に従ってファイルを作成します: _<カスタム テスト名>.test.ps1_。

テストでは、オブジェクト パラメーターまたは文字列パラメーターとしてテンプレートを取得できます。 通常はどちらか一方を使用しますが、両方を使用することもできます。

テンプレートのセクションを取得し、そのプロパティを反復処理する必要がある場合は、オブジェクト パラメーターを使用します。 オブジェクト パラメーターを使用するテストの形式は次のとおりです。

```powershell
param(
  [Parameter(Mandatory=$true,Position=0)]
  [PSObject]
  $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

テンプレート オブジェクトのプロパティは次のとおりです。

- `$schema`
- `contentVersion`
- `parameters`
- `variables`
- `resources`
- `outputs`

たとえば、`$TemplateObject.parameters` を使用してパラメーターのコレクションを取得できます。

テンプレート全体で文字列操作を行う必要がある場合は、文字列パラメーターを使用します。 文字列パラメーターを使用するテストの形式は次のとおりです。

```powershell
param(
  [Parameter(Mandatory)]
  [string]
  $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

たとえば、文字列パラメーターの正規表現を実行して、特定の構文が使用されているかどうかを確認できます。

テストの実装の詳細については、そのフォルダー内の他のテストを参照してください。

## <a name="integrate-with-azure-pipelines"></a>Azure Pipelines と統合する

テスト ツールキットを Azure パイプラインに追加できます。 パイプラインを使用すると、テンプレートが更新されるたびにテストを実行したり、デプロイ プロセスの一部として実行したりできます。

テスト ツールキットをパイプラインに追加する最も簡単な方法は、サードパーティ製の拡張機能を使用することです。 次の 2 つの拡張機能を使用できます。

- [Run ARM TTK Tests](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
- [ARM Template Tester](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

または、独自のタスクを実装することもできます。 次の例では、テスト ツールキットをダウンロードする方法を示します。

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": false,
  "alwaysRun": false,
  "displayName": "Download TTK",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
    "errorActionPreference": "stop",
    "failOnStderr": "false",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

次の例では、テストを実行する方法を示します。

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": true,
  "alwaysRun": false,
  "displayName": "Run Best Practices Tests",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
    "errorActionPreference": "continue",
    "failOnStderr": "true",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

## <a name="next-steps"></a>次のステップ

- テンプレートのテストについては、「[ARM テンプレートのテスト ケース](template-test-cases.md)」を参照してください。
- パラメーター ファイルをテストするには、「[パラメーター ファイルのテスト ケース](parameters.md)」を参照してください。
- createUiDefinition のテストについては、「[createUiDefinition.json のテスト ケース](createUiDefinition-test-cases.md)」を参照してください。
- すべてのファイルのテストについては、「[すべてのファイルのテスト ケース](all-files-test-cases.md)」を参照してください。
- テスト ツールキットの使用について説明している Microsoft Learn モジュールについては、[ARM テンプレート テスト ツールキットを使用した Azure リソースの検証](/learn/modules/arm-template-test/)に関するページを参照してください。
