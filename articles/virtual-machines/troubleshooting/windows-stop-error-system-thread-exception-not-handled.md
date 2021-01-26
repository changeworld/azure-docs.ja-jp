---
title: Windows STOP エラー - 0x0000007E システム スレッド例外が処理されませんでした
description: この記事では、ゲスト OS で問題が発生するため Azure VM を再起動したい問題を解決する手順について説明します。 メッセージでは、"システム スレッド例外が処理されませんでした" と通知されます。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681892"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows STOP エラー - 0x0000007E システム スレッド例外が処理されませんでした

この記事では、ゲスト オペレーティング システム (ゲスト OS) で問題が発生するため Azure 仮想マシン (VM) の再起動を試みる問題を解決する手順について説明します。 表示されるエラー メッセージでは、"システム スレッド例外が処理されませんでした。" と通知されます。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM 出力のスクリーンショットを表示すると、"システム スレッド例外が処理されませんでした" という停止コードまたは "0x0000007E" エラー コードのいずれかで、Windows の再起動が必要であることがわかります。

!["問題が発生したため、PC を再起動する必要があります。 自動的に再起動します。" というエラー メッセージおよび "システム スレッド例外が処理されませんでした" という停止コードで、起動中にスタックした Windows を示すスクリーンショット。](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

メモリ ダンプ ファイルが分析されるまで原因を特定することはできません。 引き続きメモリ ダンプ ファイルを収集してください。

## <a name="solution"></a>解決策

この問題を解決するには、まずそのクラッシュのメモリ ダンプ ファイルを収集してから、そのファイルを Microsoft サポートに送信する必要があります。 ダンプ ファイルを収集するには、次の 2 つのセクションの手順に従ってください。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. 修復 VM を準備するには、[VM 修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)のステップ 1 から 3 に従います。
1. リモート デスクトップ接続を使用して修復 VM に接続します。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 修復 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 たとえば、接続されている OS ディスクに割り当てられたドライブ文字のラベルが *F* である場合は、`F:\Windows` に移動します。
1. *memory.dmp* ファイルを見つけてから、メモリ ダンプ ファイルを添付して[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
1. *memory.dmp* ファイルが見つからない場合は、ガイドに従って、[マスク不可能割り込み (NMI) 呼び出しを使用してクラッシュ ダンプ ファイルを生成](/windows/client-management/generate-kernel-or-complete-crash-dump)します。

NMI 呼び出しの詳細については、[Azure シリアル コンソールでの NMI 呼び出し](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)に関するユーザー ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure 仮想マシンのブート エラーのトラブルシューティング](./boot-error-troubleshoot.md)
