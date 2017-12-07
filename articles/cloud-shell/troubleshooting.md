---
title: "Azure Cloud Shell のトラブルシューティング | Microsoft Docs"
description: "Azure Cloud Shell のトラブルシューティング"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 233569303ea3651192aafe9681f58a9582625d29
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Azure Cloud Shell のトラブルシューティング

Azure Cloud Shell での問題に対する既知の解決策は以下のとおりです。

## <a name="general-resolutions"></a>一般的な解決策

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>ストレージ ダイアログ - エラー: 403 RequestDisallowedByPolicy
- **詳細**: Cloud Shell からストレージ アカウントを作成するときに、管理者によって配置された Azure ポリシーが原因で作成が失敗します。エラー メッセージには以下が含まれます。`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **解決策**: Azure 管理者に連絡して、ストレージの作成を拒否している Azure ポリシーを削除または更新してもらいます。

### <a name="storage-dialog---error-400-disallowedoperation"></a>ストレージ ダイアログ - エラー: 400 DisallowedOperation
 - **詳細**: Azure Active Directory サブスクリプションを使うと、ストレージを作成できません。
 - **解決策**: ストレージ リソースを作成できる Azure サブスクリプションを使ってください。 Azure AD サブスクリプションでは、Azure のリソースを作成できません。

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>ターミナル出力 - エラー: Failed to connect terminal: websocket cannot be established. (ターミナルに接続できませんでした: WebSocket を確立できません。) Press `Enter` to reconnect. (再接続するには Enter キーを押してください。)
 - **詳細**: Cloud Shell では、Cloud Shell インフラストラクチャへの WebSocket 接続を確立できる必要があります。
 - **解決策**: HTTPS 要求および WebSocket 要求の *.console.azure.com のドメインへの送信を有効にするようにネットワーク設定を構成していることを確認します。

## <a name="bash-resolutions"></a>Bash の解決策

### <a name="cannot-run-az-login"></a>az login を実行できない

- **詳細**: Cloud Shell または Azure Portal へのサインインに使用するアカウントで既に認証されているため、`az login` を実行しても正しく動作しません。
- **解決策**: サインインまたはサインアウトに使用するアカウントを利用して、目的の Azure アカウントを再認証してください。

### <a name="cannot-run-the-docker-daemon"></a>Docker デーモンを実行できない

- **詳細**: Cloud Shell では、コンテナーを利用してシェル環境をホストするため、デーモンを実行することが許可されていません。
- **解決策**: 既定でインストールされている [Docker マシン](https://docs.docker.com/machine/overview/)を利用して、リモート Docker ホストから Docker コンテナーを管理します。

## <a name="powershell-resolutions"></a>PowerShell の解決策

### <a name="no-home-directory-persistence"></a>$Home ディレクトリが永続化されない

- **詳細**: アプリケーション (git、vim など) が `$Home` に書き込むデータが、PowerShell セッション間で永続化されません。
- **解決策**: PowerShell プロファイルで、`clouddrive` 内のアプリケーション固有のフォルダーへのシンボリック リンクを $Home に作成します。

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl + C キーでコマンドレット プロンプトが終了しない

- **詳細**: コマンドレットのプロンプトを終了しようとして `Ctrl+C` キーを押しても、プロンプトが終了しません。
- **解決策**: プロンプトを終了するには、`Ctrl+C` キーを押した後、`Enter` キーを押します。

### <a name="gui-applications-are-not-supported"></a>GUI アプリケーションがサポートされていない

- **詳細**: ユーザーが GUI アプリを起動しても、プロンプトが返りません。 たとえば、ユーザーが 2 要素認証が有効なプライベート GitHub リポジトリを閉じると、2 要素認証を完了するためのダイアログ ボックスが表示されます。  
- **解決策**: シェルをいったん閉じて、再び開きます。


### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online でヘルプ ページが開かない

- **詳細**: 「`Get-Help Find-Module -online`」と入力した場合、次のようなエラー メッセージが表示されます。`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **解決策**: URL をブラウザーにコピーして手動で開きます。

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM のリモート管理のトラブルシューティング

- **詳細**: WinRM に対する Windows ファイアウォールの既定の設定のため、次のようなエラー メッセージが表示されることがあります。`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **解決策**: VM が実行されていることを確認します。 `Get-AzureRmVM -Status` を実行して、VM の状態を確認できます。  次に、サブネットからの WinRM 接続を許可する新しいファイアウォール規則をリモート VM に追加します。次はその例です。

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 [Azure カスタム スクリプト拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript)を使って、リモート VM にログオンしないで新しいファイアウォール規則を追加できます。
 前述のスクリプトをファイル (`addfirerule.ps1` など) に保存し、Azure ストレージ コンテナーにアップロードできます。
 その後、次のコマンドを試します。

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` で Azure ドライブに結果をキャッシュする

- **詳細**: `dir` の結果は Azure ドライブにキャッシュされます。
- **解決策**: Azure ドライブ ビューでリソースを作成または削除した後に、`dir -force` を実行して更新します。
