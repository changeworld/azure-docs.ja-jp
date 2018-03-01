---
title: "Azure Cloud Shell の制限 | Microsoft Docs"
description: "Azure Cloud Shell の制限の概要"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 21a050373a9901220e106534464a8c13565bb3d8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell の制限

Azure Cloud Shell には、次の既知の制限があります。

## <a name="general-limitations"></a>一般的な制限事項

### <a name="system-state-and-persistence"></a>システム状態と永続化

Cloud Shell セッションを提供するマシンは一時的であり、セッションが 20 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、Azure ファイル共有がマウントされている必要があります。 そのため、Cloud Shell にアクセスするには、ご利用のサブスクリプションでストレージ リソースをセットアップできることが必要です。 その他の考慮事項:

* マウントされたストレージでは、`clouddrive` ディレクトリ内の変更のみが永続化されます。 Bash では、`$Home` ディレクトリも永続化されます。
* Azure ファイル共有は、[割り当て済みリージョン](persisting-shell-storage.md#mount-a-new-clouddrive)内からのみマウントできます。
  * Bash では、`env` を実行して、`ACC_LOCATION` として設定されたリージョンを検索します。

### <a name="browser-support"></a>ブラウザーのサポート

Cloud Shell では、Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox、および Apple Safari の最新バージョンがサポートされます。 プライベート モードの Safari はサポートされません。

### <a name="copy-and-paste"></a>コピーと貼り付け

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>特定のユーザーがアクティブにできるシェルは 1 つだけである

ユーザーは、一度に 1 種類のシェル (**Bash** または **PowerShell**) だけを起動できます。 ただし、Bash または PowerShell の複数のインスタンスを同時に実行できます。 Bash と PowerShell の間でスワップを行うと、Cloud Shell が再起動し、既存セッションが終了します。

### <a name="usage-limits"></a>Usage limits (使用状況の制限)

Cloud Shell は対話型のユース ケースを想定しています。 そのため、実行時間の長い非対話型セッションは、警告なしで終了します。

## <a name="bash-limitations"></a>Bash の制限事項

### <a name="user-permissions"></a>ユーザーのアクセス許可

権限は、sudo アクセスのない、通常のユーザーとして設定されます。 `$Home` または `clouddrive` ディレクトリの外部にあるインストールはすべて失われます。

### <a name="editing-bashrc"></a>.bashrc の編集

.bashrc を編集すると Cloud Shell の Bash で予期しないエラーが発生する可能性があるため、編集には注意が必要です。

## <a name="powershell-limitations"></a>PowerShell の制限事項

### <a name="slow-startup-time"></a>起動時間が遅い

Azure Cloud Shell (プレビュー) の PowerShell は、プレビュー期間中は、初期化に最大で 60 秒かかる場合があります。

### <a name="no-home-directory-persistence"></a>$Home ディレクトリが永続化されない

アプリケーション (たとえば、git、vim など) によって `$Home` に書き込まれたデータは、PowerShell セッション間で保持されません。 回避策については、[こちらをご覧ください](troubleshooting.md#powershell-resolutions)。

### <a name="default-file-location-when-created-from-azure-drive"></a>Azure ドライブから作成された場合の既定のファイルの場所

ユーザーは、PowerShell コマンドレットを使用して Azure ドライブにファイルを作成することができません。 Vim や Nano などの他のツールを使用して新しいファイルを作成すると、ファイルは既定で C:\Users フォルダーに保存されます。 

### <a name="gui-applications-are-not-supported"></a>GUI アプリケーションがサポートされていない

Windows ダイアログ ボックスを作成するコマンド (`Connect-AzureAD` や `Login-AzureRMAccount` など) をユーザーが実行すると、`Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` のようなエラー メッセージが表示されます。

## <a name="next-steps"></a>次の手順

[Cloud Shell のトラブルシューティング](troubleshooting.md) <br>
[Bash のクイックスタート](quickstart.md) <br>
[PowerShell のクイックスタート](quickstart-powershell.md)
