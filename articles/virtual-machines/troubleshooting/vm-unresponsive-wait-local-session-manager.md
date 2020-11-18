---
title: ローカル セッション マネージャーの待機中には VM が応答しない
description: この記事では、Azure VM の起動中に、ローカル セッション マネージャーが処理を完了するのを待ってゲスト OS がスタックする問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: ae3d35bea1c75c797447100316ceab9a27fcbe48
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423800"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>ローカル セッション マネージャーの待機中には VM が応答しない

この記事では、Azure 仮想マシン (VM) の起動中に、ローカル セッション マネージャーが処理を完了するのを待ってゲスト OS がスタックする問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットのプロンプトに次のメッセージが表示されることがわかります:"*ローカル セッション マネージャーを待機してください*"

![Windows Server 2012 R2 のスクリーンショットは、"ローカル セッション マネージャーを待機してください" というメッセージでゲスト OS がスタックしたことを示しています。](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>原因

ローカル セッション マネージャーを待機して仮想マシンがスタックする理由は複数考えられます。 ローカル セッション マネージャーの待機が繰り返し起きる問題の場合は、分析のためにメモリ ダンプを収集する必要があります。

## <a name="solution"></a>解決策

場合によっては、プロセスが完了するまで十分長く待つだけで問題が解決します。 VM が待機画面で 1 時間以上ハングする場合は、メモリ ダンプを収集した後、Microsoft サポートに連絡してください。

### <a name="collect-the-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

この問題を解決するには、まずそのクラッシュのメモリ ダンプ ファイルを収集してから、メモリ ダンプ ファイルを用意してサポートに連絡する必要があります。 ダンプ ファイルを収集するには、次の手順を行います。

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. [VM 修復コマンドのステップ 1 から 3 ](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に従い、修復 VM を準備します。
2. リモート デスクトップ接続を使用し、修復 VM に接続します。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 修復 VM で、接続されている OS ディスク内の Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が *F* の場合は、`F:\Windows` に移動する必要があります。
2. **memory.dmp** ファイルを探し、メモリ ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
3. **memory.dmp** ファイルが見つからない場合は、ガイドに従って、[マスク不可能割り込み (NMI) 呼び出しを使用してクラッシュ ダンプ ファイルを生成](/windows/client-management/generate-kernel-or-complete-crash-dump)します。

NMI 呼び出しの詳細については、[シリアル コンソールでのマスク不可能割り込み (NMI) 呼び出し](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)に関するユーザー ガイドを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure 仮想マシンのブート エラーのトラブルシューティング](boot-error-troubleshoot.md)