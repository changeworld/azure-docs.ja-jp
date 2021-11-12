---
title: Bicep の開発およびデプロイ環境のセットアップ
description: Bicep の開発環境とデプロイ環境の構成方法
ms.topic: conceptual
ms.date: 10/20/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 7f590c0d0954ca4e3ccc3f4d894f55892b01a6ae
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443511"
---
# <a name="install-bicep-tools"></a>Bicep ツールのインストール

Bicep ファイルの開発とデプロイ用に環境が設定されていることを確認しましょう。

## <a name="vs-code-and-bicep-extension"></a>VS Code と Bicep 拡張

Bicep ファイルを作成するには、適切な Bicep エディターが必要です。 以下のことが推奨されます。

- **Visual Studio Code** - まだ Visual Studio Code をインストールしていない場合は、[インストールします](https://code.visualstudio.com/)。
- **Visual Studio Code 用の Bicep 拡張機能**。  Visual Studio Code と Bicep 拡張機能によって、言語サポートとリソース オートコンプリートが提供されます。 この拡張機能は、Bicep ファイルの作成と検証に役立ちます。

  拡張機能をインストールするには、 **[拡張機能]** タブまた [Visual Studio マーケットプレース](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)で "*bicep*" を検索します。

  **[インストール]** を選択します。

  :::image type="content" source="./media/install/install-extension.png" alt-text="Bicep 拡張機能のインストール":::

拡張機能がインストールされていることを確認するには、`.bicep` ファイル拡張子を持つ任意のファイルを開きます。 右下隅にある言語モードが **Bicep** に変更されていることを確認します。

:::image type="content" source="./media/install/language-mode.png" alt-text="Bicep 言語モード":::

### <a name="troubleshoot"></a>トラブルシューティング

Visual Studio Code 用の Bicep 拡張機能をインストールするときに、次のエラー メッセージが表示される場合があります。

```error
Failed to install .NET runtime v5.0
```

```error
Failed to download .NET 5.0.x ....... Error!
```

この問題を解決するには、[.NET Web サイト](https://aka.ms/dotnet-core-download)から .NET を手動でインストールし、.NET の既存のインストールを再利用するように Visual Studio Code を構成します。 次の設定を使用:

**Windows**

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "C:\\Program Files\\dotnet\\dotnet.exe"
  }
]

```

**macOS**

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/dotnet"
  }
]
```

Visual Studio Code 設定の構成については、「[ユーザーとワークスペースの設定](https://code.visualstudio.com/docs/getstarted/settings)」を参照してください。

## <a name="deployment-environment"></a>デプロイ環境

開発環境を設定したら、デプロイ環境用のツールをインストールする必要があります。 ローカル デプロイ環境をセットアップするには、[Azure CLI](#azure-cli) または [Azure PowerShell](#azure-powershell) を使用して Bicep CLI をインストールします。 これらの手順については、次のセクションで示されています。

Azure パイプラインから Bicep ファイルをデプロイするには、「[Bicep と Azure Pipelines の統合](add-template-to-azure-pipelines.md)」をご覧ください。 GitHub Actions で Bicep ファイルをデプロイする方法については、「[GitHub Actions を使用した Bicep ファイルのデプロイ](deploy-github-actions.md)」をご覧ください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI のバージョン **2.20.0 以降** をインストールしてある必要があります。 Azure CLI をインストールまたは更新するには、以下を参照してください。

- [Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
- [Linux での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
- [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)

現在のバージョンを確認するには、次の手順を実行します。

```azurecli
az --version
```

これで、Bicep ファイルの[デプロイ](deploy-cli.md)と[逆コンパイル](decompile.md)に必要なものすべてが準備できました。 Bicep CLI は、これを必要とするコマンドを実行するときに Azure CLI によって自動的にインストールされるので、必要なものはこれですべてとなります。

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

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell のバージョン **5.6.0 以降** をインストールしてある必要があります。 更新またはインストールする方法については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

Azure PowerShell では、Bicep CLI は自動的にインストールされません。 代わりに、[Bicep CLI を手動でインストールする必要があります](#install-manually)。

> [!IMPORTANT]
> Azure CLI によってインストールされた Bicep CLI の自己完結型インスタンスは、PowerShell コマンドでは使用できません。 Bicep CLI を手動でインストールしていない場合、Azure PowerShell のデプロイは失敗します。

Bicep CLI を手動でインストールする場合は、Azure CLI の `az bicep` 構文ではなく、`bicep` 構文を使用して Bicep コマンドを実行します。

Bicep CLI のバージョンを確認するには、次を実行します。

```cmd
bicep --version
```

## <a name="install-manually"></a>手動でインストールする

次のメソッドでは、Bicep CLI をインストールし、PATH に追加します。 Azure CLI 以外を使用する場合は、手動でインストールする必要があります。

- [Linux](#linux)
- [macOS](#macos)
- [Windows](#windows)

### <a name="linux"></a>Linux

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

### <a name="macos"></a>macOS

#### <a name="via-homebrew"></a>homebrew 経由

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

#### <a name="via-bash"></a>BASH 経由

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

### <a name="windows"></a>Windows

#### <a name="windows-installer"></a>Windows インストーラー

[最新の Windows インストーラー](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)をダウンロードして実行します。 インストーラーには管理者特権は必要ありません。 インストールが完了すると、Bicep CLI がユーザーの PATH に追加されます。 PATH の変更を有効にするために、開いているコマンド シェル ウィンドウを閉じてから再度開きます。

#### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

#### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

#### <a name="manual-with-powershell"></a>PowerShell での手動

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

## <a name="install-on-air-gapped-cloud"></a>エアギャップ クラウドへのインストール

エアギャップ環境に Bicep CLI をインストールするには、Bicep CLI の実行可能ファイルを手動でダウンロードし、特定の場所に保存する必要があります。

- **Linux**

    1. **bicep-linux-x64** を、[Bicep リリース ページ](https://github.com/Azure/bicep/releases/latest/)からエアギャップではない環境にダウンロードします。
    1. 実行可能ファイルを、エアギャップ マシン上の **$HOME/.azure/bin** ディレクトリにコピーします。

- **macOS**

    1. **bicep-osx-x64** を、[Bicep リリース ページ](https://github.com/Azure/bicep/releases/latest/)からエアギャップではない環境にダウンロードします。
    1. 実行可能ファイルを、エアギャップ マシン上の **$HOME/.azure/bin** ディレクトリにコピーします。

- **Windows**

    1. **bicep-win-x64.exe** を、[Bicep リリース ページ](https://github.com/Azure/bicep/releases/latest/)からエアギャップではない環境にダウンロードします。
    1. 実行可能ファイルを、エアギャップ マシン上の **%UserProfile%/.azure/bin** ディレクトリにコピーします。

`bicep install` および `bicep upgrade` コマンドは、エアギャップ環境では機能しません。

## <a name="install-the-nightly-builds"></a>夜間ビルドのインストール

リリース前に Bicep の最新プレリリース ビットを試す場合は、[夜間ビルドのインストール](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)に関する記事を参照してください。

> [!WARNING]
> これらのプレリリース ビルドには、既知または不明なバグが含まれている可能性が非常に高くなります。

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。
