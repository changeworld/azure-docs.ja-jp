---
title: Azure Storage Explorer を使用して Azure マネージド ディスクを管理する
description: Azure Storage Explorer を使用して、リージョン間で Azure マネージド ディスクのアップロード、ダウンロード、移行を行う方法と、マネージド ディスクのスナップショットを作成する方法について説明します。
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 3206eb5b7092a43561cd114082ac567a63bff61b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130237930"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage Explorer を使用して Azure マネージド ディスクを管理する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure Storage Explorer には、次のことを行うための豊富な機能を用意しています。

- マネージド ディスクをアップロード、ダウンロード、コピーする。
- オペレーティング システムまたはデータ ディスク仮想ハード ディスクからスナップショットを作成する。
- オンプレミスから Azure にデータを移行する。
- Azure リージョン間でデータを移行する。

## <a name="prerequisites"></a>前提条件

この記事の内容を実行するには、次のものが必要です。

- Azure サブスクリプション。
- 少なくとも 1 つの Azure マネージド ディスク。
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) の最新バージョン。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

Storage Explorer を Azure に接続しないと、これを使用してリソースを管理することはできません。 このセクションで説明する手順に従って、Storage Explorer を Azure アカウントに接続します。 それ以降は、これを使用してディスクを管理できるようになります。

1. Azure Storage Explorer を開き、ツール バーの **[Connect]\(接続\)** アイコンをクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Connect]\(接続\) アイコンの位置が確認できる。](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-lrg.png#lightbox)

1. **[Connect to Azure Storage]\(Azure Storage に接続\)** ダイアログ ボックスで **[Subscription]\(サブスクリプション\)** をクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Subscription]\(サブスクリプション\) オプションの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-lrg.png#lightbox)

1. 適切な環境を選択して **[Next]\(次へ\)** をクリックします。 **[Manage custom environments]\(カスタム環境の管理\)** をクリックすれば、カスタム環境を設定、追加することもできます。

    [![Azure Storage Explorer のスクリーンショット。[Azure Environment]\(Azure 環境\) オプションの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-lrg.png#lightbox)

1. **[Sign in]\(サインイン\)** ダイアログ ボックスで Azure のサインイン情報を入力します。

    ![サインイン ダイアログ ボックスのスクリーンショット。](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. リストからサブスクリプションを選択し、 **[Open Explorer]\(Explorer を開く\)** をクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Open Explorer]\(Explorer を開く\) ボタンの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-lrg.png#lightbox)

## <a name="upload-an-on-premises-vhd"></a>オンプレミス VHD をアップロードする

オンプレミスの仮想ハード ディスク (VHD) ファイルを Azure にアップロードし、そのファイルを使用してイメージを作成できます。 このセクションで説明する手順に従ってソース ファイルをアップロードします。

1. **[Explorer]** ペインで **[Disks]\(ディスク\)** を展開し、ディスクをアップロードするリソース グループを選択します。

    [![Azure Storage Explorer のスクリーンショット。ディスクをアップロードする [Disks]\(ディスク\) のノードの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. リソース グループ詳細ペインで **[Upload]\(アップロード\)** をクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Upload]\(アップロード\) ボタンの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-lrg.png#lightbox)

1. **[Upload VHD]\(VHD のアップロード\)** ダイアログ ボックスで、VHD のソース ファイル、ディスクの名前、オペレーティング システムの種類、ディスクをアップロードするリージョン、アカウントの種類を指定します。 可用性ゾーンをサポートしているリージョンでは、好みのゾーンを選択できます。 **[作成]** を選択し、ディスクのアップロードを開始します。

    [![Azure Storage Explorer の [Upload VHD]\(VHD のアップロード\) ダイアログ ボックスのスクリーンショット。](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-lrg.png#lightbox)

1. アップロードの状態が **[アクティビティ]** に表示されます。

    [![Azure Storage Explorer のスクリーンショット。アップロードの状態のメッセージを表示する [Activities]\(アクティビティ\) ペインの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-lrg.png#lightbox)

アップロードを終えても **[Activities]\(アクティビティ\)** ペインにディスクが表示されない場合は、 **[Refresh]\(更新\)** をクリックします。

## <a name="download-a-managed-disk"></a>マネージド ディスクをダウンロードする

このセクションで説明する手順に従って、マネージド ディスクをオンプレミスの VHD にダウンロードします。 ディスクをダウンロードするには、ディスクの状態が **[Unattached]\(未接続\)** である必要があります。

1. **[Explorer]** ペインで **[Disks]\(ディスク\)** を展開し、ディスクのダウンロード元になるリソース グループを選択します。

    [![Azure Storage Explorer のスクリーンショット。ディスクをダウンロードする [Disks]\(ディスク\) のノードの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. リソース グループ詳細ペインでダウンロードするディスクを選択します。
1. **[ダウンロード]** を選択し、ディスクを保存する場所を選択します。

    [![Azure Storage Explorer のスクリーンショット。[Download]\(ダウンロード\) ボタンの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-lrg.png#lightbox)

1. **[Save]\(保存\)** をクリックしてダウンロードを開始します。 ダウンロードの状態が **[Activities]\(アクティビティ\)** に表示されます。

    [![Azure Storage Explorer のスクリーンショット。ダウンロードの状態のメッセージを表示する [Activities]\(アクティビティ\) ペインの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-lrg.png#lightbox)

## <a name="copy-a-managed-disk"></a>マネージド ディスクをコピーする

Storage Explorer を使うと、リージョン内またはリージョン間でマネージド ディスクをコピーできます。 ディスクをコピーするには:

1. **[Explorer]** ペインで **[Disks]\(ディスク\)** ドロップダウン メニューを展開し、コピーするディスクが存在するリソース グループを選択します。

    [![Azure Storage Explorer のスクリーンショット。ディスクをコピーする [Disks]\(ディスク\) のノードの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. リソース グループ詳細ペインで、コピーするディスクを選択して **[Copy]\(コピー\)** をクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Copy]\(コピー\) ボタンの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-lrg.png#lightbox)

1. **[Explorer]** ペインで **[Disks]\(ディスク\)** を展開し、コピーしたディスクを貼り付けるリソース グループを選択します。

    [![Azure Storage Explorer のスクリーンショット。コピーしたディスクを貼り付ける [Disks]\(ディスク\) のノードの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-lrg.png#lightbox)

1. リソース グループ詳細ペインで **[Paste]\(貼り付け\)** をクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Paste]\(貼り付け\) ボタンの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-lrg.png#lightbox)

1. **[Paste Disk]\(ディスクの貼り付け\)** ダイアログ ボックスで値を入力します。 そのリージョンでサポートされていれば、可用性ゾーンも指定できます。

    [![Azure Storage Explorer の [Paste Disk]\(ディスクの貼り付け\) フォームのスクリーンショット。](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-lrg.png#lightbox)

1. **[Paste]\(貼り付け\)** をクリックしてディスクのコピーを開始します。 **[Activities]\(アクティビティ\)** に状態が表示されます。

    [![Azure Storage Explorer のスクリーンショット。コピーと貼り付けの状態のメッセージを表示する [Activities]\(アクティビティ\) ペインの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-lrg.png#lightbox)

## <a name="create-a-snapshot"></a>スナップショットの作成

1. **[Explorer]** ペインで **[Disks]\(ディスク\)** を展開し、スナップショットを作成するディスクが存在するリソース グループを選択します。

    [![Azure Storage Explorer のスクリーンショット。ディスクのスナップショットを作成する [Disks]\(ディスク\) のノードの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. リソース グループ詳細ペインで、スナップショットを作成するディスクを選択して **[Create Snapshot]\(スナップショットの作成\)** をクリックします。

    [![Azure Storage Explorer のスクリーンショット。[Create Snapshot]\(スナップショットの作成\) ボタンの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-lrg.png#lightbox)

1. **[Create Snapshot]\(スナップショットの作成\)** で、スナップショットの名前と、スナップショットを作成するリソース グループを指定します。 **[作成]** を選択します。

    [![Azure Storage Explorer の　[Create Snapshot]\(スナップショットの作成\) ダイアログ ボックスのスクリーンショット。](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-lrg.png#lightbox)

1. スナップショットを作成した後、 **[Activities]\(アクティビティ\)** で **[Open in Portal]\(ポータルで開く\)** をクリックすれば、Azure portal でそのスナップショットを見ることができます。

    [![Azure Storage Explorer のスクリーンショット。スナップショットの状態のメッセージを表示する [Activities]\(アクティビティ\) ペイン上のリンクの位置を強調表示している。](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-lrg.png#lightbox)

## <a name="next-steps"></a>次のステップ

- [Azure portal で VHD から仮想マシンを作成する](./windows/create-vm-specialized-portal.md)
- [Azure portal で マネージド データ ディスクを Windows 仮想マシンに接続する](./windows/attach-managed-disk-portal.md)