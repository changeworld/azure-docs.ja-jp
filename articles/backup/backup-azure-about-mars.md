---
title: MARS エージェントについて
description: MARS エージェントでサポートされるバックアップ シナリオについて説明します
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896877"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントについて

この記事では、Microsoft Azure Recovery Services (MARS) エージェントを使用して、Azure Backup サービスでファイルまたはフォルダー、ボリューム、システム状態のいずれかをオンプレミスのコンピューターから Azure にバックアップおよび復元する方法について説明します。

MARS エージェントでは、次のバックアップ シナリオがサポートされています。

![Recovery Services コンテナーのダッシュボード](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **ファイルとフォルダー**: Windows のファイルとフォルダーを選択的に保護します。
- **ボリューム レベル**: ご使用のマシンの Windows ボリューム全体を保護します。
- **システム レベル**: Windows システム状態全体を保護します。

MARS エージェントでは、次の復元シナリオがサポートされています。

![Recovery Services コンテナーのダッシュボード](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **同じサーバー**:バックアップが最初に作成されたのとサーバーと同じサーバー。
    -    **ファイルとフォルダー**:復元するファイルやフォルダーを個別に参照して選択することができます。
    -    **ボリューム レベル**:復元するボリュームと回復ポイントを選択し、同じ場所または同じマシン上の別の場所に復元することができます。  既存のファイルのコピーを作成する、既存のファイルを上書きする、または既存のファイルの回復をスキップするのいずれかが行えます。
    -    **システム レベル**:システム状態と回復ポイントを選択して、指定した場所にある同じマシンに復元することができます。


-   **代替サーバー**:バックアップが作成されたサーバーとは別のサーバー。
    -    **ファイルとフォルダー**:回復ポイントをターゲット マシンに復元するファイルやフォルダーを個別に参照して選択することができます。
    -    **ボリューム レベル**:別の場所に復元するボリュームと回復ポイントを選択するには、既存のファイルのコピーを作成する、既存のファイルを上書きする、または既存のファイルの回復をスキップするのいずれかを行います。
    -    **システム レベル**:システム状態と回復ポイントを選択して、システム状態ファイルとして別のマシンに復元することができます。

## <a name="backup-process"></a>バックアップ プロセス

1.  Azure portal から [Recovery Service コンテナー](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)を作成し、[バックアップの目標] からファイルとフォルダー、またはシステム状態 (またはその両方) を選択します。
2.  Recovery Service コンテナーの資格情報とエージェント インストーラーをオンプレミスのマシンに[ダウンロード](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)します。 バックアップ オプションを選択してオンプレミスのマシンを保護するには、ファイル、フォルダー、システム状態を選択し、MARS エージェントをダウンロードします。
3.  インフラストラクチャを準備します。

    a.    インストーラーを実行して、エージェントを[インストール](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)します。

    b.  ダウンロードしたコンテナーの資格情報を使用して、マシンを Recovery Services コンテナーに登録します。
4.  クライアントのエージェント コンソールから、[バックアップのスケジュール](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy)を使用してバックアップを構成します。 バックアップ データの保持ポリシーを指定し、保護を開始します。

![Recovery Services コンテナーのダッシュボード](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>その他のシナリオ
-   **Azure VM 内の特定のファイルとフォルダーをバックアップする** - Azure 仮想マシン (VM) をバックアップする主な方法は、VM で Azure Backup 拡張機能を使用することです。 これにより、VM 全体がバックアップされます。 VM 内の特定のファイルとフォルダーをバックアップする場合は、Azure VM に MARS エージェントをインストールできます。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)。

-   **オフライン シード処理** - Azure へのデータの初回完全バックアップでは通常、大量のデータが転送されます。その後の差分または増分のみを転送するバックアップと比べると、多くのネットワーク帯域幅が必要です。 Azure Backup では、初回バックアップが圧縮されます。 オフライン シード処理プロセスによって、Azure Backup でディスクを使用し、圧縮済みの初回バックアップ データをオフラインで Azure にアップロードすることができます。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。


## <a name="next-steps"></a>次の手順
[MARS エージェントのサポート マトリックス](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS エージェントの FAQ](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
