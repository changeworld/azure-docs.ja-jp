---
title: Windows STOP エラー ステータス システム プロセスが停止しました
description: この記事では、オペレーティング システムで STOP エラー 0xC000021A が発生し、Azure 仮想マシンの起動ができなくなる問題を解決するための手順を示します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b13b61aff819271ed1722572f251f9a6d14b17ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976999"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows STOP エラー 0xC000021A ステータス システム プロセスが停止しました

この記事では、オペレーティング システム (OS) で STOP エラー 0xC000021A が発生し、Azure 仮想マシン (VM) が起動しなくなる問題を解決するための手順を示します。

## <a name="symptom"></a>症状

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、起動時に OS でエラーが発生したことを示すメッセージと共に、次のメッセージがスクリーンショットに表示されます。

"**問題が発生したため、PC を再起動する必要があります。エラー情報を収集しています。再起動できます。(##% 完了) 詳細については、次のエラーを後からオンラインで検索してください:0xC000021a**"。

  ![図 1 には、次のメッセージが表示されたエラー コード #0xC000021A が表示されています "問題が発生したため、PC を再起動する必要があります。 エラー情報を収集しています。再起動できます。"。](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>原因

エラー 0xC000021A は **STATUS_SYSTEM_PROCESS_TERMINATED** を意味します。

このエラーは、WinLogon (winlogon.exe) やクライアント サーバー ランタイム サブシステム (csrss.exe) などの重要なプロセスが失敗した場合に発生します。 これらのサービスのいずれかが停止したことがカーネルによって検出されると、**STOP 0xC000021A** エラーが発生します。 このエラーには、次のようないくつかの原因が考えられます。

- 一致しないシステム ファイルがインストールされている。
- Service Pack または KB 更新のインストールに失敗した。
- ハード ディスクの復元に使用されるバックアップ プログラムで、使用されている可能性があるファイルを正しく復元できなかった。
- 互換性のないサードパーティ プログラムがインストールされている。

## <a name="solution"></a>解決策

### <a name="collect-the-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

この問題を解決するには、クラッシュ ダンプを分析する必要があります。 クラッシュのメモリ ダンプ ファイルを収集し、サポートにお問い合わせください。 ダンプ ファイルを収集するには、次の手順を行います。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1.  [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。
2.  **リモート デスクトップ接続**を使用して、修復 VM に接続します。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1.  復旧 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が F の場合は、F:\Windows に移動します。
2.  memory.dmp ファイルを検索し、メモリ ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
3.  memory.dmp ファイルが見つからない場合は、代わりに[シリアル コンソールでマスク不可能割り込み (NMI) 呼び出し](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)を使用することをお勧めします。 このガイドに従い、[ここで NMI 呼び出しを使用してクラッシュ ダンプ ファイルを生成](/windows/client-management/generate-kernel-or-complete-crash-dump)できます。

## <a name="next-steps"></a>次の手順

- 詳細なトラブルシューティング情報については、[一般的なブート エラーのトラブルシューティング](./boot-error-troubleshoot.md)に関するページ、または [OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う方法](./troubleshoot-recovery-disks-windows.md)に関するページを参照してください。 さらに、[ブート診断を使用して仮想マシンのトラブルシューティングを行う方法](./boot-diagnostics.md)について理解しておくことも必要です。
- Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」をご覧ください。
- VM に接続できない場合は、[Azure VM への RDP 接続のトラブルシューティング](./troubleshoot-rdp-connection.md)に関するページを参照してください。