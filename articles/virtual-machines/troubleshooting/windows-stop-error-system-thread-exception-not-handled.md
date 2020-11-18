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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423796"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows STOP エラー - 0x0000007E システム スレッド例外が処理されませんでした

この記事では、ゲスト OS で問題が発生するため Azure VM を再起動したい問題を解決する手順について説明します。 メッセージでは、"システム スレッド例外が処理されませんでした" と通知されます。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、スクリーンショットに、停止コード "**システム スレッド例外が処理されませんでした**" またはエラー コード **0x0000007E** のいずれかと共に、Windows の再起動が必要であると表示されていることがわかります。

![スクリーンショットには、次のメッセージで、ブート中に Windows がスタックしたことが示されています。"問題が発生したため、PC を再起動する必要があります。 自動的に再起動します。" 停止コード: "システム スレッド例外が処理されませんでした"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

メモリ ダンプ ファイルが分析されるまで原因を特定することはできません。 引き続きメモリ ダンプ ファイルを収集してください。

## <a name="solution"></a>解決策

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
> [Azure 仮想マシンのブート エラーのトラブルシューティング](./boot-error-troubleshoot.md)