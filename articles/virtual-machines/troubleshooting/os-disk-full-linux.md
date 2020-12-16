---
title: Linux 仮想マシンのフル OS ディスクに関する問題
description: Linux 仮想マシンのフル OS ディスクに関する問題を解決する方法
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523308"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Linux 仮想マシンのフル OS ディスクに関する問題

Linux 仮想マシン (VM) の OS ディスクがいっぱいになると、VM の適切な操作に問題が発生する可能性があります。

## <a name="symptom"></a>症状

新しいファイルを作成しようとすると、次のようなメッセージが表示されます。

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

複数のデーモンは、ブート セッション中に一時ファイルを作成できないことを示します。

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>原因

このエラー メッセージが表示される原因はいくつかあります。

1. ディスクがいっぱいになっている可能性があります。
1. ファイルシステムで iNode が不足している可能性があります。
1. データ ディスクが既存のディレクトリにマウントされたため、ファイルが非表示になることがあります。
1. プロセスで開かれたファイルが削除されています。

## <a name="solution"></a>解決策

### <a name="process-overview"></a>プロセスの概要

1. 修復 VM を作成してアクセスします。
1. ディスク上の領域を解放します。
1. VM を再構築します。

> [!NOTE]
> このエラーが発生する場合、ゲスト OS は動作しなくなります。 この問題を解決するには、オフライン モードでこの問題のトラブルシューティングを行います。

### <a name="create-and-access-a-repair-vm"></a>修復 VM の作成とアクセス

1. [仮想マシンの修復コマンド](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
1. SSH を使用して修復 VM に接続します。

### <a name="free-up-space-on-the-disk"></a>ディスクの空き領域を増やす

この問題を解決するには、次のようにします。

- ディスクがまだ最大サイズの 1 TB になっていない場合は、1 TB になるまでサイズを変更します。
- ディスクに空き領域を作成します。

1. ディスクがいっぱいかどうかを確認します。 ディスクのサイズが 1 TB 未満の場合は、[Azure CLI を使用](../linux/expand-disks.md)して最大 1 TB まで拡張します。
1. ディスクが既に 1 TB の場合は、ディスクに空き領域を作成する必要があります。
1. サイズ変更とクリーンアップが完了したら、VM を再構築します。

### <a name="rebuild-the-vm"></a>VM を再構築する

[VM 修復コマンドの手順 5](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) に従って VM を再構築します。
