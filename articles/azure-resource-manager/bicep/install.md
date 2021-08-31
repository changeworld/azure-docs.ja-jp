---
title: Bicep の開発およびデプロイ環境のセットアップ
description: Bicep の開発環境とデプロイ環境の構成方法
ms.topic: conceptual
ms.date: 07/19/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 297cafd074beb6c1535747be98dd664041d7b497
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453374"
---
# <a name="install-bicep-tools"></a>Bicep ツールのインストール

Bicep ファイルの開発とデプロイ用に環境が設定されていることを確認しましょう。

## <a name="development-environment"></a>開発環境

Bicep ファイルを作成するには、適切な Bicep エディターが必要です。 以下のことが推奨されます。

- **Visual Studio Code** - まだ Visual Studio Code をインストールしていない場合は、[インストールします](https://code.visualstudio.com/)。
- **Visual Studio Code 用の Bicep 拡張機能**。  Visual Studio Code と Bicep 拡張機能によって、言語サポートとリソース オートコンプリートが提供されます。 この拡張機能は、Bicep ファイルの作成と検証に役立ちます。

  拡張機能をインストールするには、 **[拡張機能]** タブまた [Visual Studio マーケットプレース](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)で "*bicep*" を検索します。

  **[インストール]** を選択します。

  :::image type="content" source="./media/install/install-extension.png" alt-text="Bicep 拡張機能のインストール":::

拡張機能がインストールされていることを確認するには、`.bicep` ファイル拡張子を持つ任意のファイルを開きます。 右下隅にある言語モードが **Bicep** に変更されていることを確認します。

:::image type="content" source="./media/install/language-mode.png" alt-text="Bicep 言語モード":::

## <a name="deployment-environment"></a>デプロイ環境

Bicep ファイルをデプロイするために必要なコマンドを取得する最も簡単な方法は、最新バージョンの Azure CLI をインストールする方法です。 PowerShell も使用できますが、追加のインストールが必要です。

### <a name="azure-cli"></a>Azure CLI

Azure CLI バージョン 2.20.0 以降がインストールされている必要があります。 Azure CLI をインストールまたは更新するには、以下を参照してください。

- [Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
- [Linux での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
- [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)

現在のバージョンを確認するには、次の手順を実行します。

```azurecli
az --version
```

これで、Bicep ファイルの[デプロイ](deploy-cli.md)と[逆コンパイル](decompile.md)に必要なものすべてが準備できました。 Azure CLI 2.20.0 以降では、必要なコマンドが実行されると Bicep CLI が自動的にインストールされるため、すべてが揃います。

Bicep CLI のインストールを手動で開始するには、次のコマンドを使用します。

```azurecli
az bicep install
```

最新バージョンにアップグレードするには、以下を使用します。

```azurecli
az bicep upgrade
```

インストールの有効性を検証するには、次を使用します。

```azurecli
az bicep version
```

その他のコマンドについては、[Bicep CLI](bicep-cli.md) に関するページを参照してください。

> [!IMPORTANT]
> Azure CLI によって、Bicep CLI の自己完結型インスタンスがインストールされます。 このインスタンスは、手動でインストールしたバージョンと競合することはありません。 Azure CLI は、Bicep CLI を PATH に追加しません。

### <a name="powershell"></a>PowerShell

Azure PowerShell モジュール バージョン 5.6.0 以降がインストールされている必要があります。 更新またはインストールする方法については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

Azure PowerShell では、Bicep CLI は自動的にインストールされません。 代わりに、[Bicep CLI を手動でインストールする必要があります](#install-manually)。

> [!IMPORTANT]
> Azure CLI によってインストールされた Bicep CLI の自己完結型インスタンスは、PowerShell コマンドでは使用できません。 Bicep CLI を手動でインストールしていない場合、Azure PowerShell のデプロイは失敗します。

Bicep CLI を手動でインストールする場合は、Azure CLI の `az bicep` 構文ではなく、`bicep` 構文を使用して Bicep コマンドを実行します。

Bicep ファイルをデプロイするには、Bicep CLI バージョン 0.3.1 以降を使用します。 Bicep CLI のバージョンを確認するには、次を実行します。

```cmd
bicep --version
```

### <a name="install-manually"></a>手動でインストールする

次のメソッドでは、Bicep CLI をインストールし、PATH に追加します。 Azure CLI 以外を使用する場合は、手動でインストールする必要があります。

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!
```

> [!NOTE]
> [Alpine](https://alpinelinux.org/) のような軽量 Linux ディストリビューションの場合は、前のスクリプトで **bicep-linux-x64** ではなく **bicep-linux-musl-x64** を使用します。

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>homebrew を使用

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="via-bash"></a>BASH 経由

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows インストーラー

[最新の Windows インストーラー](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)をダウンロードして実行します。 インストーラーには管理者特権は必要ありません。 インストールが完了すると、Bicep CLI がユーザーの PATH に追加されます。 PATH の変更を有効にするために、開いているコマンド シェル ウィンドウを閉じてから再度開きます。

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>PowerShell での手動

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

### <a name="install-the-nightly-builds"></a>夜間ビルドのインストール

リリース前に Bicep の最新プレリリース ビットを試す場合は、[夜間ビルドのインストール](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)に関する記事を参照してください。

> [!WARNING]
> これらのプレリリース ビルドには、既知または不明なバグが含まれている可能性が非常に高くなります。

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
