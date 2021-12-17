---
title: Azure portal - マネージド ディスクに対するインポートおよびエクスポートのアクセスを制限する
description: Azure portal を使用して、マネージド ディスクの Private Link を有効にします。 これにより、仮想ネットワーク内でディスクを安全にエクスポートおよびインポートできます。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/03/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 57eef48a188f07847efcda31b4cd6a6043f2ae2a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491015"
---
# <a name="restrict-importexport-access-for-managed-disks-using-azure-private-link"></a>Azure Private Link を使用してマネージド ディスクに対するインポートおよびエクスポートのアクセスを制限する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、マネージド ディスクのエクスポートとインポートを制限し、Azure 仮想ネットワーク上のクライアントから[プライベート リンク](../private-link/private-link-overview.md)を介してデータにより安全にアクセスできます。 プライベート エンドポイントでは、対象のマネージド ディスクのために仮想ネットワークのアドレス空間の IP アドレスが使用されます。 仮想ネットワーク上のクライアントとマネージド ディスク間のネットワーク トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベート リンク経由でのみ送信され、パブリック インターネットから公開されることはなくなります。

Private Link を使用してマネージド ディスクをエクスポートおよびインポートするには、ディスク アクセス リソースを作成した後、プライベート エンドポイントを作成することによってこれを同じサブスクリプション内の仮想ネットワークにリンクします。 次に、ディスクまたはスナップショットをディスク アクセスのインスタンスに関連付けます。

## <a name="limitations"></a>制限事項

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="create-a-disk-access-resource"></a>ディスク アクセス リソースを作成する

1. Azure portal にサインインし、[このリンク](https://aka.ms/disksprivatelinks)から **[ディスク アクセス]** に移動します。

    > [!IMPORTANT]
    > [ディスク アクセス\] ペインに移動するには、[提供されているリンク](https://aka.ms/disksprivatelinks)を使用する必要があります。 現時点では、このリンクを使用しないと、パブリック ポータルに表示されません。

1. **[+ 作成]** を選択して、新しいディスク アクセス リソースを作成します。
1. **[ディスク アクセスの作成]** ウィンドウ、サブスクリプションとリソース グループを選択します。 **[インスタンスの詳細]** で、名前を入力し、リージョンを選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="ディスク アクセス作成ウィンドウのスクリーンショット。目的の名前を入力し、リージョンを選択し、リソース グループを選択して続行します":::

1. **[確認および作成]** を選択します。
1. リソースが作成されたら、直接そのリソースに移動します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="ポータルの [リソースに移動] ボタンのスクリーンショット":::

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

次に、プライベート エンドポイントを作成してディスク アクセス用に構成する必要があります。

1. ディスク アクセス リソースの **[設定]** で、 **[プライベート エンドポイントの接続]** を選択します。
1. **[+ プライベート エンドポイント]** を選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="ディスク アクセス リソースの概要ウィンドウのスクリーンショット。[プライベート エンドポイントの接続] が強調表示されています。":::

1. **[プライベート エンドポイントの作成]** ウィンドウで、リソース グループを選択します。
1. 名前を入力し、ディスク アクセス リソースが作成されたのと同じリージョンを選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="プライベート エンドポイント作成ワークフローの最初のウィンドウのスクリーンショット。適切なリージョンを選択しない場合は、後で問題が発生する可能性があります。":::

1. **[Next:リソース]** を選択します。
1. **[リソース]** ペインで、 **[マイ ディレクトリ内の Azure リソースに接続します]** を選択します。
1. **[リソースの種類]** で、 **[Microsoft.Compute/diskAccesses]** を選択します。
1. **[リソース]** で、前の手順で作成したディスク アクセス リソースを選択します。
1. **[対象サブリソース]** は **[ディスク]** のままにします。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="プライベート エンドポイント作成ワークフローの 2 番目のペインのスクリーンショット。すべての値が強調表示されています ([リソースの種類]、[リソース]、[対象サブリソース])":::

1. **[次へ: 構成]** を選択します。
1. ディスクのインポートとエクスポートを制限する仮想ネットワークを選択します。 これにより、他の仮想ネットワークへのディスクのインポートとエクスポートが防止されます。

    > [!NOTE]
    > 選択したサブネットで有効なネットワーク セキュリティ グループがある場合、それはこのサブネットのプライベート エンドポイントに対してのみ無効になります。 このサブネット上の他のリソースでは、ネットワーク セキュリティ グループの適用が維持されます。

1. 適切なサブネットを選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="プライベート エンドポイント作成ワークフローの 3 番目のペインのスクリーンショット。仮想ネットワークとサブネットが強調されています。":::

1. **[確認および作成]** を選択します。

## <a name="enable-private-endpoint-on-your-disk"></a>ディスク上でプライベート エンドポイントを有効にする

1. 構成するディスクに移動します。
1. **[設定]** で **[ネットワーク]** を選択します。
1. **[Private endpoint (through disk access)]\(プライベート エンドポイント (ディスク アクセス経由)\)** を選択し、前の手順で作成したディスク アクセスを選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="マネージド ディスクの [ネットワーク] ペインのスクリーンショット。プライベート エンドポイントの選択と、選択したディスク アクセスが強調表示されています。これを保存すると、このアクセス用にディスクが構成されます。":::

1. **[保存]** を選択します。

これで、マネージド ディスクのインポートとエクスポートに使用できるプライベート リンクが構成されました。

## <a name="next-steps"></a>次のステップ

- VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする - [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) または [Azure PowerShell モジュール](windows/disks-upload-vhd-to-managed-disk-powershell.md)
- VHD をダウンロードする - [Windows](windows/download-vhd.md) または [Linux](linux/download-vhd.md)
- [プライベート リンクとマネージド ディスクに関する FAQ](./faq-for-disks.yml)
- [PowerShell を使用して別のリージョンのストレージ アカウントにマネージド スナップショットを VHD としてエクスポートまたはコピーする](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)
