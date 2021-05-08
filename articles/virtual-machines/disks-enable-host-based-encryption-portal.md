---
title: ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure portal - マネージド ディスク
description: ホストでの暗号化を使用して、Azure マネージド ディスク (Azure portal) でエンドツーエンドの暗号化を有効にします。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721870"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure portal を使用して、ホストでの暗号化を使用したエンドツーエンドの暗号化を有効にする

ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化とその他のマネージド ディスクの暗号化の概要については、次の記事を参照してください。

* Linux: [ホストでの暗号化 - ご利用の VM データのエンドツーエンド暗号化](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

* Windows: [ホストでの暗号化 - ご利用の VM データのエンドツーエンド暗号化](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>前提条件

VM または VMSS に対して EncryptionAtHost プロパティを使用する前に、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、サブスクリプションに対して機能を有効にしてください。

1. **Azure ポータル**:[Azure portal](https://portal.azure.com) から Cloud Shell アイコンを選択します。

    ![Azure portal から Cloud Shell を起動するアイコン](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  次のコマンドを実行して、サブスクリプションに対して機能を登録します

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  この機能を試す前に、下のコマンドを使用して、登録状態が Registered であることを確認してください (数分かかります)。

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


[提供されているリンク](https://aka.ms/diskencryptionupdates)を使用して Azure portal にサインインします。

> [!IMPORTANT]
> [提供されたリンク](https://aka.ms/diskencryptionupdates)を使用して Azure portal にアクセスする必要があります。 現時点では、このリンクを使用しないと、ホストでの暗号化がパブリックの Azure portal に表示されません。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault とディスク暗号化セットを作成する

機能が有効になったら、Azure Key Vault とディスク暗号化セットを設定する必要があります (まだ行っていない場合)。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM をデプロイする

ホストでの暗号化を有効にするには、新しい VM をデプロイ必要があります。既存の VM で有効にすることはできません。

1. 「**Virtual Machines**」で検索し、 **[+ 追加]** を選択して、VM を作成します。
1. 新しい仮想マシンを作成し、適切なリージョンとサポートされている VM サイズを選択します。
1. 必要に応じて **[基本]** ブレードでその他の値を入力した後、 **[ディスク]** ブレードに進みます。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="[仮想マシンの作成] の [基本] ブレードのスクリーンショット、リージョンと VM サイズが強調表示されています。":::

1. **[ディスク]** ブレードの **[ホストでの暗号化]** で **[はい]** を選択します。
1. 必要に応じて、残りの選択を行います。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="仮想マシンの作成のディスク ブレードのスクリーンショット、ホストでの暗号化が強調表示されています。":::

1. VM のデプロイ プロセスを完了し、ご使用の環境に合った選択を行います。

これで、ホストでの暗号化を有効にした VM をデプロイしました。それに関連付けられているすべてのディスクが、ホストでの暗号化を使用して暗号化されます。

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
