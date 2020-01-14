---
title: 再起動中、シャットダウン中、またはサービスの停止中に Azure Virtual Machines の再起動が停止する | Microsoft Docs
description: この記事は、Azure Windows Virtual Machines のサービス エラーのトラブルシューティングに役立ちます。
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: db7b26402170236843891799738088b9229e4693
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474193"
---
# <a name="azure-windows-vm-restart-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>"再起動中"、"シャットダウン中"、または "サービスの停止中" に Azure Windows VM の再起動が停止する

この記事では、Microsoft Azure で Windows 仮想マシン (VM) を再起動するときに、"再起動中"、"シャットダウン中"、または "サービスを停止しています" というメッセージが表示される可能性がある問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[起動診断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を利用して VM のスクリーンショットを表示すると、スクリーンショットに "再起動中"、"シャットダウン中"、または "サービスを停止しています" というメッセージが表示されることがあります。

!["再起動中"、"シャットダウン中"、および "サービスを停止しています" 画面](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>原因

Windows では、シャットダウン プロセスを使用してシステム メンテナンス操作が実行され、更新、ロール、機能などの変更が処理されます。 この重要なプロセスは、完了するまで中断しないことをお勧めします。 更新/変更の数と VM のサイズによっては、プロセスに時間がかかることがあります。 プロセスが停止している場合は、OS が破損している可能性があります。 プロセスを中断するのは、プロセスに時間がかかりすぎている場合のみにします。

## <a name="solution"></a>解決策

### <a name="collect-a-process-memory-dump"></a>プロセスのメモリ ダンプの収集

1. 同じリージョンから作業用 VM にアタッチされている新規または既存のデータ ディスクに、[Procdump ツール](http://download.sysinternals.com/files/Procdump.zip)をダウンロードします。

2. 必要なファイルが含まれているディスクを作業用 VM からデタッチし、破損した VM にそのディスクを接続します。 このディスクは**ユーティリティ ディスク**と呼ばれています。

[シリアル コンソール](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows)を使用して、次の手順を完了します。

1. 管理 PowerShell を開き、停止中にハングしたサービスを確認します。

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 管理 CMD で、ハングしたサービスの PID を取得します。

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. ハングしたプロセス <STOPPING SERVICE> からメモリ ダンプのサンプルを取得します。

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. ここでハングしたプロセスを中止して、シャット ダウン プロセスのロックを解除します。

   ``
   taskkill /PID <PID> /t /f
   ``

OS が再起動し、正常に起動したら、あとは OS の整合性を確認するだけです。 破損が報告された場合は、ディスクの破損がなくなるまで、次のコマンドを実行します。

``
dism /online /cleanup-image /restorehealth
``

プロセスのメモリ ダンプを収集できない場合、またはこの問題が再帰的で、根本原因を分析する必要がある場合は、以下の OS のメモリ ダンプの収集に進み、サポート リクエストを開いてください。

### <a name="collect-an-os-memory-dump"></a>OS のメモリ ダンプの収集

変更が処理されるまで待っても問題が解決されないときは、場合によっては、メモリ ダンプ ファイルを収集し、サポートに問い合わせる必要があります。 ダンプ ファイルを収集するには、次の手順を実行します。

**復旧 VM に OS ディスクを接続する**

1. バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)に関する記事を参照してください。

2. [復旧 VM に OS ディスクを接続します](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)。

3. 復旧 VM にリモート デスクトップ接続します。

4. OS ディスクが暗号化されている場合、次の手順に進む前に暗号化をオフにする必要があります。 詳細については、[起動できない VM で暗号化されている OS ディスクを復号する](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)方法に関するページを参照してください。

**ダンプ ファイルを探してサポート チケットを送信する**

1. 復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が F の場合は、F:\Windows に移動する必要があります。

2. memory.dmp ファイルを探し、ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。

ダンプ ファイルが見つからない場合は、ダンプ ログとシリアル コンソールを有効にする次の手順に進みます。

**ダンプ ログとシリアル コンソールを有効にする**

ダンプ ログとシリアル コンソールを有効にするには、次のスクリプトを実行します。

1. 管理者特権のコマンド プロンプト セッション ([管理者として実行]) を開きます。

2. 次のスクリプトを実行します。

   このスクリプトでは、接続されている OS ディスクに割り当てられているドライブ文字が F であると想定しています。実際の VM の適切な値に置き換えてください。

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
   
   REM Enable Serial Console
   bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
   bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
   bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
   bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
   bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   
   REM Suggested configuration to enable OS Dump
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. この VM に選択しているサイズに応じて、RAM と同じサイズのメモリを割り当てることができる十分な領域がディスク上にあることを確認します。

4. 十分な領域がない場合、または VM が大きい場合は (G、GS、または E シリーズ)、このファイルが作成される場所を変更し、VM に接続されている他のデータ ディスクに任せることができます。 場所を変更するには、次のキーを変更する必要があります。

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [OS ディスクをデタッチし、影響を受ける VM に OS ディスクを再アタッチします](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)。

6. VM を起動し、シリアル コンソールにアクセスします。

7. [マスク不可能割り込み (NMI) を送信する] を選択し、メモリ ダンプをトリガーします。

   ![マスク不可能割り込みを送信する](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. OS ディスクを復旧 VM にもう一度アタッチし、ダンプ ファイルを収集します。

## <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

ダンプ ファイルを収集した後、根本原因を判断するために Microsoft サポートに問い合わせてください。
