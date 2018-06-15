---
title: Azure Cloud Shell のトラブルシューティング | Microsoft Docs
description: Azure Cloud Shell のトラブルシューティング
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: cffa67509690f4c594182fbe8104f0620da56bee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608952"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Azure Cloud Shell のトラブルシューティングと制限事項

Azure Cloud Shell に関する問題のトラブルシューティングを行うための既知の解決策は以下のとおりです。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング

### <a name="early-timeouts-in-firefox"></a>FireFox での早期タイムアウト
- **詳細**: Cloud Shell は開いている WebSocket を使ってお使いのブラウザーに入力/出力を渡します。 FireFox には WebSocket を途中で閉じることができる事前設定されたポリシーがあり、Cloud Shell の早期タイムアウトの原因になります。
- **解決策**: FireFox を開き、URL で "about:config" に移動します。 "network.websocket.timeout.ping.request" を検索し、値を 0 から 10 に変更します。

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>ストレージ ダイアログ - エラー: 403 RequestDisallowedByPolicy
- **詳細**: Cloud Shell からストレージ アカウントを作成するときに、管理者によって配置された Azure ポリシーが原因で作成が失敗します。エラー メッセージには以下が含まれます。`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **解決策**: Azure 管理者に連絡して、ストレージの作成を拒否している Azure ポリシーを削除または更新してもらいます。

### <a name="storage-dialog---error-400-disallowedoperation"></a>ストレージ ダイアログ - エラー: 400 DisallowedOperation
 - **詳細**: Azure Active Directory サブスクリプションを使うと、ストレージを作成できません。
 - **解決策**: ストレージ リソースを作成できる Azure サブスクリプションを使ってください。 Azure AD サブスクリプションでは、Azure のリソースを作成できません。

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>ターミナル出力 - エラー: Failed to connect terminal: websocket cannot be established. (ターミナルに接続できませんでした: WebSocket を確立できません。) Press `Enter` to reconnect. (再接続するには Enter キーを押してください。)
 - **詳細**: Cloud Shell では、Cloud Shell インフラストラクチャへの WebSocket 接続を確立できる必要があります。
 - **解決策**: HTTPS 要求および WebSocket 要求の *.console.azure.com のドメインへの送信を有効にするようにネットワーク設定を構成していることを確認します。

## <a name="bash-troubleshooting"></a>Bash のトラブルシューティング

### <a name="cannot-run-the-docker-daemon"></a>Docker デーモンを実行できない

- **詳細**: Cloud Shell では、コンテナーを利用してシェル環境をホストするため、デーモンを実行することが許可されていません。
- **解決策**: 既定でインストールされている [Docker マシン](https://docs.docker.com/machine/overview/)を利用して、リモート Docker ホストから Docker コンテナーを管理します。

## <a name="powershell-troubleshooting"></a>PowerShell のトラブルシューティング

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

## <a name="general-limitations"></a>一般的な制限事項
Azure Cloud Shell には、次の既知の制限があります。

### <a name="system-state-and-persistence"></a>システム状態と永続化

Cloud Shell セッションを提供するマシンは一時的であり、セッションが 20 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、Azure ファイル共有がマウントされている必要があります。 そのため、Cloud Shell にアクセスするには、ご利用のサブスクリプションでストレージ リソースをセットアップできることが必要です。 その他の考慮事項:

* マウントされたストレージでは、`clouddrive` ディレクトリ内の変更のみが永続化されます。 Bash では、`$Home` ディレクトリも永続化されます。
* Azure ファイル共有は、[割り当て済みリージョン](persisting-shell-storage.md#mount-a-new-clouddrive)内からのみマウントできます。
  * Bash では、`env` を実行して、`ACC_LOCATION` として設定されたリージョンを検索します。
* Azure Files は、ローカル冗長ストレージと geo 冗長ストレージのアカウントのみをサポートします。

### <a name="browser-support"></a>ブラウザーのサポート

Cloud Shell では、Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox、および Apple Safari の最新バージョンがサポートされます。 プライベート モードの Safari はサポートされません。

### <a name="copy-and-paste"></a>コピーと貼り付け

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>特定のユーザーがアクティブにできるシェルは 1 つだけである

ユーザーは、一度に 1 種類のシェル (**Bash** または **PowerShell**) だけを起動できます。 ただし、Bash または PowerShell の複数のインスタンスを同時に実行できます。 Bash と PowerShell の間でスワップを行うと、Cloud Shell が再起動し、既存セッションが終了します。

### <a name="usage-limits"></a>Usage limits (使用状況の制限)

Cloud Shell は対話型のユース ケースを想定しています。 そのため、実行時間の長い非対話型セッションは、警告なしで終了します。

## <a name="bash-limitations"></a>Bash の制限事項

### <a name="user-permissions"></a>ユーザーのアクセス許可

権限は、sudo アクセスのない、通常のユーザーとして設定されます。 `$Home` ディレクトリ外のインストールはすべて失われます。

### <a name="editing-bashrc"></a>.bashrc の編集

.bashrc を編集すると Cloud Shell で予期しないエラーが発生する可能性があるため、編集には注意が必要です。

## <a name="powershell-limitations"></a>PowerShell の制限事項

### <a name="slow-startup-time"></a>起動時間が遅い

Azure Cloud Shell (プレビュー) の PowerShell は、プレビュー期間中は、初期化に最大で 60 秒かかる場合があります。

### <a name="default-file-location-when-created-from-azure-drive"></a>Azure ドライブから作成された場合の既定のファイルの場所

ユーザーは、PowerShell コマンドレットを使用して Azure ドライブにファイルを作成することができません。 Vim や Nano などの他のツールを使用して新しいファイルを作成すると、ファイルは既定で C:\Users フォルダーに保存されます。 

### <a name="gui-applications-are-not-supported"></a>GUI アプリケーションがサポートされていない

Windows ダイアログ ボックスを作成するコマンド (`Connect-AzureAD` や `Connect-AzureRmAccount` など) をユーザーが実行すると、`Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` のようなエラー メッセージが表示されます。

## <a name="gdpr-compliance-for-cloud-shell"></a>Cloud Shell の GDPR コンプライアンス

Azure Cloud Shell では、お客様の個人情報を慎重に取り扱っています。Azure Cloud Shell サービスによって取得および保存されたデータは、最近使用したシェル、選択されたフォント サイズ、選択されたフォントの種類、clouddrive の基盤となるファイル共有の詳細など、エクスペリエンスの既定値を設定するために使用されます。 このデータをエクスポートまたは削除したい場合は、以下の手順に従ってください。

### <a name="export"></a>エクスポート
選択されたシェル、フォント サイズ、フォントの種類など、Cloud Shell によって保存されるユーザー設定を**エクスポート**するには、次のコマンドを実行します。

1. Cloud Shell で Bash を起動する
2. 次のコマンドを実行します。
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>削除
選択されたシェル、フォント サイズ、フォントの種類など、Cloud Shell によって保存されるユーザー設定を**削除**するには、次のコマンドを実行します。 次回、Cloud Shell を起動すると、再びファイル共有にオンボードするように求めるメッセージが表示されます。 

ユーザー設定を削除しても、実際の Azure Files 共有は削除されません。Azure Files に移動して、その操作を完了します。

1. Cloud Shell で Bash を起動する
2. 次のコマンドを実行します。
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
