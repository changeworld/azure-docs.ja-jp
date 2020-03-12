---
title: MARS エージェントについて
description: MARS エージェントでサポートされるバックアップ シナリオについて説明します
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673288"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントについて

この記事では、Azure Backup サービスが Microsoft Azure Recovery Services (MARS) エージェントを使用して、ファイル、フォルダー、およびボリュームまたはシステム状態をオンプレミスのコンピューターから Azure にバックアップおよび復元する方法について説明します。

MARS エージェントでは、次のバックアップ シナリオがサポートされています。

![MARS のバックアップ シナリオ](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **ファイルとフォルダー**:Windows のファイルとフォルダーを選択的に保護します。
- **ボリューム レベル**:ご使用のマシンの Windows ボリューム全体を保護します。
- **システム レベル**:Windows システム状態全体を保護します。

MARS エージェントでは、次の復元シナリオがサポートされています。

![MARS の復旧シナリオ](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **同じサーバー**:バックアップが最初に作成されたサーバー。
  - **ファイルとフォルダー**:復元する個々のファイルとフォルダーを選択します。
  - **ボリューム レベル**:復元するボリュームと回復ポイントを選択して、同じ場所または同じマシン上の別の場所に復元します。  既存のファイルのコピーを作成するか、既存のファイルを上書きするか、既存のファイルの回復をスキップします。
  - **システム レベル**:システム状態と回復ポイントを選択して、指定した場所にある同じマシンに復元します。

- **代替サーバー**:バックアップが作成されたサーバー以外のサーバー。
  - **ファイルとフォルダー**:回復ポイントをターゲットマシンに復元する、個々のファイルとフォルダーを選択します。
  - **ボリューム レベル**:別の場所に復元する、ボリュームと回復ポイントを選択します。 既存のファイルのコピーを作成するか、既存のファイルを上書きするか、既存のファイルの回復をスキップします。
  - **システム レベル**:システム状態ファイルとして別のマシンに復元する、システム状態と回復ポイントを選択します。

## <a name="backup-process"></a>バックアップ プロセス

1. Azure portal から [Recovery Service コンテナー](install-mars-agent.md#create-a-recovery-services-vault)を作成し、[Backup goals]\(バックアップの目標\) からファイル、フォルダー、およびシステム状態を選択します。
2. オンプレミスのマシンに [Recovery Service コンテナーの資格情報とエージェント インストーラーをダウンロードします](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)。

    オンプレミスのマシンを保護するために [バックアップ] オプションを選択し、ファイル、フォルダー、およびシステム状態を選択して、MARS エージェントをダウンロードします。

3. インフラストラクチャを準備します。

    a. インストーラーを実行して、[エージェントをインストールします](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)。

    b. ダウンロードしたコンテナーの資格情報を使用して、マシンを Recovery Services コンテナーに登録します。
4. クライアントのエージェント コンソールから、[バックアップを構成します](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)。 バックアップ データの保持ポリシーを指定して、保護を開始します。

![Azure Backup エージェントの図](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>その他のシナリオ

- **Azure 仮想マシン内の特定のファイルやフォルダーをバックアップ**:Azure 仮想マシン (VM) をバックアップする主な方法は、VM 上で Azure Backup 拡張機能を使用することです。 この拡張機能は、VM 全体をバックアップします。 VM 内の特定のファイルやフォルダーをバックアップする場合は、Azure VM に MARS エージェントをインストールできます。 詳細については、「[アーキテクチャ:組み込みの Azure VM バックアップ](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)」を参照してください。

- **オフライン シード処理**:通常、Azure へのデータの初回完全バックアップでは大量のデータが転送されるため、より広いネットワーク帯域幅が必要になります。 後続のバックアップでは、差分または増分のデータ量のみが転送されます。 Azure Backup では、初回バックアップが圧縮されます。 *オフライン シード処理*プロセスによって、Azure Backup ではディスクを使用して、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。 詳細については、「[Azure Data Box を使用した Azure Backup オフライン バックアップ](offline-backup-azure-data-box.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[MARS エージェントのサポート マトリックス](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS エージェントに関する FAQ](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
