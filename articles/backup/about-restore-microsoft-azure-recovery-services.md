---
title: Microsoft Azure Recovery Services (MARS) エージェントの復元オプション
description: Microsoft Azure Recovery Services (MARS) エージェントで使用できる復元オプションについて説明します。
ms.reviewer: mepand
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: d2d6868c787080ddd78b574d1b20434fa27d8574
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250333"
---
# <a name="about-restore-using-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントを使用した復元について 

この記事では、Microsoft Azure Recovery Services (MARS) エージェントで使用できる復元オプションについて説明します。

## <a name="before-you-begin"></a>開始する前に

- [MARS エージェント](https://aka.ms/azurebackup_agent)の最新バージョンがインストールされていることを確認します。
- [ネットワーク帯域幅の調整](backup-windows-with-mars-agent.md#enable-network-throttling)が無効になっていることを確認します。
- [エージェントのキャッシュ フォルダー](./backup-azure-file-folder-backup-faq.yml)用の十分な空き容量がある高速ストレージが利用可能であることを確認します。
- メモリと CPU リソースを監視し、データの圧縮解除と復号化に使用できるリソースが十分にあることを確認します。
- **インスタント リストア** 機能を使用して回復ポイントをディスクとしてマウントする際は、マルチスレッド コピー オプション (/MT スイッチ) がある **robocopy** を使用して、マウントされた回復ポイントからファイルを効率的にコピーします。

## <a name="restore-options"></a>復元オプション

MARS エージェントには、複数の復元オプションが用意されています。 各オプションには、特定のシナリオに適した、独自の利点があります。

MARS エージェントを使用すると、次のことができます。

- **[Windows Server のシステム状態のバックアップを復元](backup-azure-restore-system-state.md):** このオプションを使用すると、Azure Backup の回復ポイントからシステム状態をファイルとして復元し、Windows Server バックアップ ユーティリティを使用して Windows Server に適用することができます。  
- **[ボリューム内のすべてのバックアップ ファイルを復元](restore-all-files-volume-mars.md):** このオプションを使用すると、Azure Backup の回復ポイントから、指定されたボリュームのバックアップ データをすべて回復します。 これにより、転送速度が向上します (最大 40 MBPS)。<br>大量のデータやボリューム全体を回復する場合は、このオプションを使用することをお勧めします。
- **[PowerShell を使用してボリューム内の指定したバックアップ ファイルとフォルダーのセットを復元](backup-client-automation.md#restore-data-from-azure-backup):** ボリューム ルートに対するファイルおよびフォルダーへの相対的なパスがわかっている場合、このオプションを使用すると、ボリューム全体の復元の転送速度で、指定したファイルとフォルダーのセットを回復ポイントから復元できます。 ただし、このオプションでは、[インスタント リストア] オプションを使用して回復ポイント内のファイルとフォルダーを参照する利便性は得られません。
- **[インスタント リストアを使用して個々のファイルとフォルダーを復元](backup-azure-restore-windows-server.md):** このオプションを使用すると、回復ポイントのボリュームをドライブとしてマウントすることで、バックアップ データにすばやくアクセスできます。 その後、ファイルとフォルダーを参照したりコピーしたりできます。 このオプションでは、最大 6 MBPS の速度でコピーを行うことができます。これは、合計サイズが 80 GB 未満の個々のファイルとフォルダーを回復する場合に最適です。 必要なファイルをコピーできたら、回復ポイントのマウントを解除できます。

## <a name="next-steps"></a>次のステップ

- よく寄せられる質問については、[MARS エージェントに関する FAQ](backup-azure-file-folder-backup-faq.yml) に関するページを参照してください。
- サポートされるシナリオと制限事項については、[MARS エージェントを使用したバックアップのサポート マトリックス](backup-support-matrix-mars-agent.md)に関するページを参照してください。
