---
title: Azure Cloud Shell のトラブルシューティング | Microsoft Docs
description: Azure Cloud Shell のトラブルシューティング
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: f954e726aabb34f74d81580e3afc08f2b0b9914b
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619408"
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

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>TLS 1.2 を使用したサポートのための Cloud Shell 接続の設定
 - **詳細**: Cloud Shell への接続用の TLS のバージョンを定義するには、ブラウザー固有の設定を行う必要があります。
 - **解決策**: ブラウザーのセキュリティ設定に移動して、[TLS 1.2 の使用] の横にあるチェック ボックスをオンにします。

## <a name="bash-troubleshooting"></a>Bash のトラブルシューティング

### <a name="cannot-run-the-docker-daemon"></a>Docker デーモンを実行できない

- **詳細**: Cloud Shell では、コンテナーを利用してシェル環境をホストするため、デーモンを実行することが許可されていません。
- **解決策**: 既定でインストールされている [Docker マシン](https://docs.docker.com/machine/overview/)を利用して、リモート Docker ホストから Docker コンテナーを管理します。

## <a name="powershell-troubleshooting"></a>PowerShell のトラブルシューティング

### <a name="gui-applications-are-not-supported"></a>GUI アプリケーションがサポートされていない

- **詳細**: ユーザーが GUI アプリケーションを起動しても、プロンプトが返りません。 たとえば、2 要素認証が有効なプライベート GitHub リポジトリを複製すると、2 要素認証を完了するためのダイアログ ボックスが表示されます。
- **解決策**: シェルをいったん閉じて、再び開きます。

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Azure VM のリモート管理のトラブルシューティング

- **詳細**: WinRM に対する Windows ファイアウォールの既定の設定のため、次のようなエラー メッセージが表示されることがあります。`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **解決策**: `Enable-AzureRmVMPSRemoting` を実行して、ターゲット コンピューター上での PowerShell リモート処理のすべての側面を有効にします。

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` によって Azure ドライブで結果が更新されない

- **詳細**: 既定では、ユーザー エクスペリエンスを確保する最適化のために、`dir` の結果が Azure ドライブにキャッシュされます。
- **解決策**: Azure リソースを作成、更新、または削除した後に、`dir -force` を実行して Azure ドライブで結果を更新します。

## <a name="general-limitations"></a>一般的な制限事項

Azure Cloud Shell には、次の既知の制限があります。

### <a name="system-state-and-persistence"></a>システム状態と永続化

Cloud Shell セッションを提供するマシンは一時的であり、セッションが 20 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、Azure ファイル共有がマウントされている必要があります。 そのため、Cloud Shell にアクセスするには、ご利用のサブスクリプションでストレージ リソースをセットアップできることが必要です。 その他の考慮事項:

- マウントされたストレージでは、`clouddrive` ディレクトリ内の変更のみが永続化されます。 Bash では、`$HOME` ディレクトリも永続化されます。
- Azure ファイル共有は、[割り当て済みリージョン](persisting-shell-storage.md#mount-a-new-clouddrive)内からのみマウントできます。
  - Bash では、`env` を実行して、`ACC_LOCATION` として設定されたリージョンを検索します。
- Azure Files は、ローカル冗長ストレージと geo 冗長ストレージのアカウントのみをサポートします。

### <a name="browser-support"></a>ブラウザーのサポート

Cloud Shell では、以下のブラウザーの最新バージョンがサポートされます。

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - プライベート モードの Safari はサポートされません。

### <a name="copy-and-paste"></a>コピーと貼り付け

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>特定のユーザーがアクティブにできるシェルは 1 つだけである

ユーザーは、一度に 1 種類のシェル (**Bash** または **PowerShell**) だけを起動できます。 ただし、Bash または PowerShell の複数のインスタンスを同時に実行できます。 Bash と PowerShell の間でスワップを行うと、Cloud Shell が再起動し、既存セッションが終了します。

### <a name="usage-limits"></a>Usage limits (使用状況の制限)

Cloud Shell は対話型のユース ケースを想定しています。 そのため、実行時間の長い非対話型セッションは、警告なしで終了します。

### <a name="user-permissions"></a>ユーザーのアクセス許可

権限は、sudo アクセスのない、通常のユーザーとして設定されます。 `$Home` ディレクトリ外のインストールはすべて失われます。

## <a name="bash-limitations"></a>Bash の制限事項

### <a name="editing-bashrc"></a>.bashrc の編集

.bashrc を編集すると Cloud Shell で予期しないエラーが発生する可能性があるため、編集には注意が必要です。

## <a name="powershell-limitations"></a>PowerShell の制限事項

### <a name="preview-version-of-azuread-module"></a>AzureAD モジュールのプレビュー バージョン

現時点では、.NET Standard ベースのモジュールのプレビュー バージョンである `AzureAD.Standard.Preview` を入手できます。 このモジュールは、`AzureAD` と同じ機能を提供します。

### <a name="sqlserver-module-functionality"></a>`SqlServer` モジュールの機能

Cloud Shell に含まれている `SqlServer` モジュールには、PowerShell Core に対するプレリリース サポートしかありません。 特に、`Invoke-SqlCmd` はまだ使用できません。

### <a name="default-file-location-when-created-from-azure-drive"></a>Azure ドライブから作成された場合の既定のファイルの場所

ユーザーは、PowerShell コマンドレットを使用して Azure ドライブにファイルを作成することができません。 ユーザーが vim や nano などの他のツールを使用して新しいファイルを作成すると、これらのファイルは、既定では `$HOME` に保存されます。

### <a name="commands-that-create-gui-pop-ups-are-not-supported"></a>GUI ポップアップを作成するコマンドがサポートされていない

Windows ダイアログ ボックスを作成するコマンド (`Connect-AzureAD` や `Connect-AzureRmAccount` など) をユーザーが実行すると、`Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` のようなエラー メッセージが表示されます。

### <a name="tab-completion-can-throw-psreadline-exception"></a>タブ補完によって PSReadline 例外がスローされることがある

ユーザーの PSReadline EditMode が Emacs に設定されており、そのユーザーがタブ補完ですべての可能性を表示しようとしたが、ウィンドウ サイズが小さすぎてすべての可能性を表示できない場合、PSReadline はハンドルされない例外をスローします。

### <a name="large-gap-after-displaying-progress-bar"></a>進行状況バーを表示した後に大きなギャップができる

コマンドまたはユーザー アクション (`Azure:` ドライブ内にある状態でのタブ補完など) によって進行状況バーが表示される場合は、カーソルが正しく設定されていないために、前に進行状況バーがあった場所にギャップが現れることがあります。

### <a name="random-characters-appear-inline"></a>ランダムな文字がインラインで表示される

ユーザー入力にカーソル位置のシーケンス コード (`5;13R` など) が表示される場合があります。 これらの文字は手動で削除できます。

## <a name="personal-data-in-cloud-shell"></a>Cloud Shell での個人データ

Azure Cloud Shell は、ユーザーの個人データを慎重に取り扱います。Azure Cloud Shell サービスによって取得および格納されたデータは、最近使用されたシェル、推奨されるフォント サイズ、推奨されるフォントの種類、クラウド ドライブの基盤となるファイル共有の詳細などの、ユーザー エクスペリエンスのための既定値を提供するために使用されます。 このデータをエクスポートまたは削除したい場合は、以下の手順に従ってください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>エクスポート
選択されたシェル、フォント サイズ、フォントの種類など、Cloud Shell によって保存されるユーザー設定を**エクスポート**するには、次のコマンドを実行します。

1. [![](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)
2. Bash または PowerShell で次のコマンドを実行します。

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>削除
選択されたシェル、フォント サイズ、フォントの種類など、Cloud Shell によって保存されるユーザー設定を**削除**するには、次のコマンドを実行します。 次回、Cloud Shell を起動すると、再びファイル共有にオンボードするように求めるメッセージが表示されます。 

>[!Note]
> ユーザー設定を削除しても、実際の Azure Files 共有は削除されません。 Azure Files に移動して、そのアクションを完了します。

1. [![](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)
2. Bash または PowerShell で次のコマンドを実行します。

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
