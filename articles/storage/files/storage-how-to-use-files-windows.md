---
title: SMB Azure ファイル共有を Windows でマウントする | Microsoft Docs
description: Windows と Windows Server での Azure ファイル共有の使用方法について説明します。 オンプレミスまたは Azure VM で実行されている Windows インストールで、SMB 3.x を使用して Azure ファイル共有を利用します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/10/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0cf24c5196d1cdd9fc7418e93cdfaf47cfdebf30
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241347"
---
# <a name="mount-smb-azure-file-share-on-windows"></a>SMB Azure ファイル共有を Windows でマウントする
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows と Windows Server でシームレスに使うことができます。 この記事では、Windows と Windows Server で Azure ファイル共有を使う際の注意点について取り上げます。

Azure ファイル共有がホストされている Azure リージョンの外の Azure ファイル共有 (オンプレミスの共有、他の Azure リージョン内の共有など) をパブリック エンドポイント経由で使用するために、OS は SMB 3.x をサポートする必要があります。 それより前のバージョンの、SMB 2.1 しかサポートされない Windows からパブリック エンドポイント経由で Azure ファイル共有をマウントすることはできません。

| Windows のバージョン | SMB のバージョン | Azure Files SMB マルチチャネル | SMB チャンネルの最大暗号化 |
|-|-|-|-|
| Windows Server 2022 | SMB 3.1.1 | Yes | AES-256-GCM |
| Windows 11 | SMB 3.1.1 | Yes | AES-256-GCM |
| Windows 10 バージョン 21H1 | SMB 3.1.1 | KB5003690 以降はサポート | AES-128-GCM |
| Windows Server バージョン 20H2 | SMB 3.1.1 | KB5003690 以降はサポート | AES-128-GCM |
| Windows 10、バージョン 20H2 | SMB 3.1.1 | KB5003690 以降はサポート | AES-128-GCM |
| Windows Server バージョン 2004 | SMB 3.1.1 | KB5003690 以降はサポート | AES-128-GCM |
| Windows 10 バージョン 2004 | SMB 3.1.1 | KB5003690 以降はサポート | AES-128-GCM |
| Windows Server 2019 | SMB 3.1.1 | KB5003703 以降はサポート | AES-128-GCM |
| Windows 10 Version 1809 | SMB 3.1.1 | KB5003703 以降はサポート | AES-128-GCM |
| Windows Server 2016 | SMB 3.1.1 | KB5004238 以降と[適用されたレジストリ キー](#windows-server-2016-and-windows-10-version-1607)はサポート | AES-128-GCM |
| Windows 10 Version 1607 | SMB 3.1.1 | KB5004238 以降と[適用されたレジストリ キー](#windows-server-2016-and-windows-10-version-1607)はサポート | AES-128-GCM |
| Windows 10 バージョン 1507 | SMB 3.1.1 | KB5004249 以降と[適用されたレジストリ キー](#windows-10-version-1507)はサポート | AES-128-GCM |
| Windows Server 2012 R2 | SMB 3.0 | いいえ | AES-128-CCM |
| Windows 8.1 | SMB 3.0 | いいえ | AES-128-CCM |
| Windows Server 2012 | SMB 3.0 | いいえ | AES-128-CCM |
| Windows Server 2008 R2<sup>1</sup> | SMB 2.1 | いいえ | サポートなし |
| Windows 7<sup>1</sup> | SMB 2.1 | いいえ | サポートされていません |

<sup>1</sup>Windows 7 および Windows Server 2008 R2 に対する Microsoft の通常のサポートは終了しました。 [拡張セキュリティ更新 (ESU) プログラム](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)を介してのみ、セキュリティ更新プログラムの追加サポートを購入できます。 これらのオペレーティング システムから移行することを強くお勧めします。

> [!Note]  
> 常に、各 Windows バージョンの最新のサポート技術情報を参照することをお勧めします。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>前提条件 
ポート 445 が開いていることを確認します。SMB プロトコルでは、TCP ポート 445 が開いている必要があります。ポート 445 がブロックされている場合は、接続が失敗します。 ポート 445 がファイアウォールでブロックされているかどうかは、`Test-NetConnection` コマンドレットで確認できます。 ブロックされた 445 ポートを回避する方法については、「[原因 1:ポート 445 がブロックされている](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)」セクション (Windows トラブルシューティング ガイド) を参照してください。

## <a name="using-an-azure-file-share-with-windows"></a>Windows で Azure ファイル共有を使用する
Windows で Azure ファイル共有を使用するには、Azure ファイル共有にドライブ文字 (マウント ポイントのパス) を割り当ててマウントするか、または対応する [UNC パス](/windows/win32/fileio/naming-a-file)経由でアクセスする必要があります。 

この記事では、ストレージ アカウント キーを使用してファイル共有にアクセスします。 ストレージ アカウント キーは、アクセスするファイル共有内のファイルとフォルダーすべてに対する管理者アクセス許可を含んだストレージ アカウントの管理者キーであると共に、ストレージ アカウントに格納されているすべてのファイル共有および他のストレージ リソース (BLOB、キュー、テーブルなど) の管理者キーでもあります。 それで対応できないワークロードについては、[Azure File Sync](../file-sync/file-sync-planning.md) または [SMB 経由の ID ベースの認証](storage-files-active-directory-overview.md)を使用できます。

SMB ファイル共有が想定されている基幹業務 (LOB) アプリケーションを Azure にリフトアンドシフトする一般的なパターンは、専用の Windows ファイル サーバーを Azure VM で実行する代わりとして Azure ファイル共有を使うことです。 基幹業務アプリケーションで Azure ファイル共有を使うための移行に関して、その作業を成功させるうえで重要な考慮事項があります。多くの基幹業務アプリケーションは、VM の管理者アカウントではなく、制限されたシステム アクセス許可を与えられた専用のサービス アカウントのコンテキストで実行されるということです。 そのため、Azure ファイル共有の資格情報をマウント/保存する際は、自分の管理者アカウントからではなく、必ずサービス アカウントのコンテキストから行う必要があります。

### <a name="mount-the-azure-file-share"></a>Azure ファイル共有をマウントする

Azure portal には、ファイル共有をホストに直接マウントするために使用できるスクリプトが用意されています。 この用意されているスクリプトを使用することをお勧めします。

このスクリプトを入手するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. マウントするファイル共有が含まれているストレージ アカウントに移動します。
1. **[ファイル共有]** を選択します。
1. マウントするファイル共有を選択します。

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="[ファイル共有] ブレードのスクリーンショット。ファイル共有が強調表示されています。":::

1. **[接続]** を選択します。

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="ファイル共有の接続アイコンのスクリーンショット。":::

1. 共有のマウント先のドライブ文字を選択します。
1. 表示されたスクリプトをコピーします。

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="[接続] ブレードのスクリーンショット。スクリプトのコピー ボタンが強調表示されています。":::

1. ファイル共有をマウントするホスト上のシェルにスクリプトを貼り付けて実行します。

これで、Azure ファイル共有がマウントされました。

### <a name="mount-the-azure-file-share-with-file-explorer"></a>エクスプローラーを使用した Azure ファイル共有のマウント
> [!Note]  
> 以下の手順は Windows 10 で操作する場合であり、それ以前のリリースでは少し異なることがある点に注意してください。 

1. エクスプローラーを開きます。 [スタート] メニューから開くことも、Windows + E キーを押すショートカットで開くこともできます。

1. ウィンドウの左側の **[PC]** に移動します。 これで、リボンで使用できるメニューが変更されます。 [コンピューター] メニューの **[ネットワーク ドライブの割り当て]** を選択します。
    
    ![[ネットワーク ドライブの割り当て] ドロップダウン メニューのスクリーンショット](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. ドライブ文字を選択し、UNC パスを入力します。UNC パスの形式は `\\<storageAccountName>.file.core.windows.net\<fileShareName>` です (例: `\\anexampleaccountname.file.core.windows.net\example-share-name`)。
    
    ![[ネットワーク ドライブの割り当て] ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. ユーザー名として先頭に `AZURE\` を付けたストレージ アカウント名を使用し、パスワードとしてストレージ アカウント キーを使用します。
    
    ![ネットワーク資格情報ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Azure ファイル共有を自由に使用します。
    
    ![Azure ファイル共有がマウントされました](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Azure ファイル共有をマウント解除することになったら、エクスプローラーの **[ネットワークの場所]** の下にある共有のエントリを右クリックし、 **[切断]** を選択します。

### <a name="accessing-share-snapshots-from-windows"></a>Windows から共有スナップショットへのアクセス
手動で、またはスクリプトや Azure Backup のようなサービスを通じて自動で共有スナップショットを取得した場合、Windows のファイル共有内にある以前のバージョンの共有、ディレクトリ、または特定のファイルを表示することができます。 共有スナップショットは、[Azure PowerShell](./storage-how-to-use-files-portal.md)、[Azure CLI](./storage-how-to-use-files-portal.md)、または [Azure portal](storage-how-to-use-files-portal.md) を使用して取得できます。

#### <a name="list-previous-versions"></a>以前のバージョンを一覧表示する
復元が必要な項目または親項目を参照します。 ダブルクリックして、目的のディレクトリに移動します。 右クリックして、メニューから **[プロパティ]** を選択します。

![選択したディレクトリの右クリック メニュー](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

**[以前のバージョン]** を選択して、このディレクトリの共有スナップショットの一覧を表示します。 ネットワークの速度とディレクトリ内の共有のスナップショットの数に応じて、一覧の読み込みに数秒かかる場合があります。

![[以前のバージョン] タブ](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

**[開く]** を選択して、特定のスナップショットを開くことができます。 

![開かれたスナップショット](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>以前のバージョンから復元する
共有スナップショット作成時のディレクトリ全体の内容を元の場所に再帰的にコピーするには、 **[復元]** を選択します。

 ![警告メッセージ内の [復元] ボタン](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="enable-smb-multichannel"></a>SMB マルチチャネルを有効にする
Azure Files で SMB マルチチャネルをサポートするには、Windows に関連するすべてのパッチを適用して最新の状態にする必要があります。 Windows Server 2016、Windows 10 バージョン 1607、Windows 10 バージョン 1507 を含むいくつかの古い Windows バージョンでは、完全にパッチが適用されたインストールで関連するすべての SMB マルチチャネルの修正を適用するために、追加のレジストリ キーを設定する必要があります。 これらの 3 つのバージョンより新しい Windows バージョンを実行している場合は、追加のアクションは必要ありません。

### <a name="windows-server-2016-and-windows-10-version-1607"></a>Windows Server 2016 および Windows 10 バージョン 1607
Windows Server 2016 および Windows 10 バージョン 1607 の SMB マルチチャネルの修正をすべて有効にするには、次の PowerShell コマンドを実行します。

```PowerShell
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Policies\Microsoft\FeatureManagement\Overrides" `
    -Name "2291605642" `
    -Value 1 `
    -Force
```

### <a name="windows-10-version-1507"></a>Windows 10 バージョン 1507
Windows 10 バージョン 1507 の SMB マルチチャネルの修正をすべて有効にするには、次の PowerShell コマンドを実行します。

```PowerShell
Set-ItemProperty `
    -Path "HKLM:\SYSTEM\CurrentControlSet\Services\MRxSmb\KBSwitch" `
    -Name "{FFC376AE-A5D2-47DC-A36F-FE9A46D53D75}" `
    -Value 1 `
    -Force
```

## <a name="next-steps"></a>次のステップ
Azure Files の詳細については、次のリンクをご覧ください。
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [FAQ](./storage-files-faq.md)
- [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)