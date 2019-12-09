---
title: Azure Linux Virtual Machines のブート エラーのトラブルシューティング | Microsoft Docs
description: この記事には、Azure Linux Virtual Machines のブート エラーのトラブルシューティングに関する記事へのリンクがあります。
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408561"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Azure Linux Virtual Machines のブート エラーのトラブルシューティング

この記事では、Microsoft Azure で Linux 仮想マシン (VM) を起動するときに発生する可能性がある一般的なブート エラーの一覧を示します。 エラーについて詳しくては、「**ブート エラーと解決策**」セクションをご覧ください。

## <a name="boot-errors-and-solutions"></a>ブート エラーと解決策

* [GRUB の復旧](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>次の手順

- [VM シリアル コンソール](serial-console-linux.md)

Azure で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う:

- [Azure VM の修復](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 ディスクのスワップ: これは次のいずれかを使用して自動化できます。
- [Power Shell の回復スクリプト](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash の回復スクリプト](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>ディスクのスワップの動画:

GRUB にアクセスできない場合は、お使いの VM をディスクのスワップを行って自動的に復旧する手順を[この](https://youtu.be/m5t0GZ5oGAc)動画で確認してください。

## <a name="unofficial-solution"></a>非公式の解決方法

サポート対象外のベータ スクリプト [ALAR](https://github.com/malachma/azure-auto-recover) で VM の復旧を試行することもできます。