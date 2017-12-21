---
title: "Azure Cloud Shell (プレビュー) での PowerShell の機能 | Microsoft Docs"
description: "Azure Cloud Shell での PowerShell の機能の概要"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/6/2017
ms.author: damaerte
ms.openlocfilehash: 31ba4b68850e14fc038df0bbe91146b90f8ca4b3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Azure Cloud Shell (プレビュー) での PowerShell の機能とツール

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> [Bash](features.md) の機能とツールも利用できます。

Cloud Shell (プレビュー) での PowerShell は `Windows Server 2016` 上で実行されます。

## <a name="features"></a>Features (機能)

### <a name="secure-automatic-authentication"></a>セキュリティで保護された自動認証

Cloud Shell (プレビュー) での PowerShell は、Azure PowerShell へのアクセス アカウントを安全かつ自動的に認証します。

### <a name="files-persistence-across-sessions"></a>セッション間でのファイルの永続化

セッション間でファイルを保持するために、Cloud Shell の初回起動時に、Microsoft Azure ファイル共有のアタッチについて案内されます。
Azure ファイル共有の初回アタッチが完了すると、今後すべてのセッションで、記憶域が自動的にアタッチされます (`$home\clouddrive` としてマウントされます) 。
Cloud Shell は要求に応じて一時的なマシンが割り当てられるため、`$home\clouddrive` 外のファイルやマシンの状態は、セッション間で保持されません。

[Azure ファイル共有を Cloud Shell にアタッチする方法の詳細について説明します。](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Azure ドライブ (Azure:)

Cloud Shell (プレビュー) の PowerShell は、Azure ドライブ (`Azure:`) で開始します。
Azure ドライブを使うと、ファイル システムのナビゲーションと同じように、Compute、Network、Storage などの Azure リソースを簡単に検出およびナビゲーションできるようになります。
引き続き、使い慣れた [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure)を使ってこれらのリソースを管理できます。
Azure リソースに対するすべての変更は、Azure Portal で直接行われたものも、Azure PowerShell コマンドレット経由で行われたものも、Azure ドライブに即座に反映されます。

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>コンテキストの認識

- **リソース グループのスコープ**: Azure ドライブ (`Azure:`) の特定のリソース グループ階層内にいるときは、該当のリソース グループ名が自動的に Azure PowerShell コマンドレットに渡されます。

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: このコマンドレットは、Azure ドライブ (`Azure:`) 下の現在のコンテキストで使用できるコマンドの一覧を返します。 たとえば、ユーザーが `Azure:\<subscription name>\StorageAccounts` にいるときは、ストレージ関連のコマンドのみを表示します。

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>リッチな PowerShell スクリプトの編集

PowerShell のファイル (`.ps1,.psm1,.psd1`) の編集に VIM を利用する場合、シンタックスハイライトと IntelliSense が利用できます。
IntelliSense のサポートは、VIM プラグインと [PowerShell エディター サービス](https://github.com/PowerShell/PowerShellEditorServices)のローカル インスタンスにより実現されています。

> [!TIP]
> `TAB` キーを入力すると、コマンド名、パラメーター名、およびパラメーター値 (該当する場合) の候補が表示されます (IntelliSense)。

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>拡張可能なモデル

[PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget) を使用することで、[PowerShell ギャラリー](https://www.powershellgallery.com)からカスタム モジュールとスクリプトを簡単にインストール (および更新) できます。
インストール後、モジュールは自動的に Cloud Shell セッション間で永続化されます。

> [!TIP]
> ユーザーがインストールしたモジュールは、`$Home\CloudDrive\.pscloudshell\WindowsPowerShell` フォルダーに保存されます。 このフォルダーへのシンボリック リンクが、ユーザーのドキュメント フォルダーに作成されます (`$home\Documents\WindowsPowerShell`)。

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>ゲスト VM の管理

2 つの組み込みコマンド `Enter-AzureRmVM` と `Invoke-AzureRmVMCommand` を使って、Azure VM をリモート管理できます。
これらのコマンドは、PowerShell remoting をベースとして使用しており、対象の Azure VM に対して PowerShell remoting で接続できる必要があります。

## <a name="tools"></a>ツール

|**カテゴリ**    |**名前**                                 |
|----------------|-----------------------------------------|
|Azure ツール     |[Azure PowerShell (5.1.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.1.1)<br> [Azure CLI (2.0.22)](https://docs.microsoft.com/cli/azure/overview)|
|テキスト エディター    |vim<br> nano                             |
|パッケージ マネージャー |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|ソース管理  |git                                      |
|データベース       |[SqlServer モジュール](https://www.powershellgallery.com/packages/SqlServer)<br> [sqlcmd ユーティリティ](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|テスト ツール      |Pester                                   |

## <a name="language-support"></a>言語のサポート

|**言語**|**バージョン**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 および [6.0 (ベータ)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>次のステップ

[Cloud Shell (プレビュー) での PowerShell のクイックスタート](quickstart-powershell.md)

[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/)
