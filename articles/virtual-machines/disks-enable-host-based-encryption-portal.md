---
title: ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure portal - マネージド ディスク
description: ホストでの暗号化を使用して、Azure マネージド ディスク (Azure portal) でエンドツーエンドの暗号化を有効にします。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1fd010e645370389a657f70aeb2e821b69477ef1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687571"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure portal を使用して、ホストでの暗号化を使用したエンドツーエンドの暗号化を有効にする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化とその他のマネージド ディスクの暗号化の概要については、「[ホストでの暗号化 - ご利用の VM データのエンドツーエンド暗号化](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)」を参照してください。

エンドツーエンド暗号化を有効にすると、一時ディスクとエフェメラル OS ディスクは保存時に、プラットフォーム マネージド キーを使用して暗号化されます。 OS とデータ ディスクのキャッシュは、ディスクの暗号化の種類として選択したものに応じて、カスタマー マネージド キーまたはプラットフォーム マネージド キーのいずれかを使用して保存時に暗号化されます。 たとえば、ディスクがカスタマー マネージド キーで暗号化されている場合、そのディスクのキャッシュはカスタマー マネージド キーで暗号化されます。ディスクがプラットフォーム マネージド キーで暗号化されている場合、そのディスクのキャッシュはプラットフォーム マネージド キーで暗号化されます。

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>前提条件

VM または VMSS に対して EncryptionAtHost プロパティを使用する前に、サブスクリプションに対してこの機能を有効にする必要があります。 下の手順に従って、サブスクリプションの機能を有効にします。

1. **Azure ポータル**:[Azure portal](https://portal.azure.com) から Cloud Shell アイコンを選択します。

    ![Azure portal から Cloud Shell を起動するアイコン](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
1.  次のコマンドを実行して、サブスクリプションに対して機能を登録します

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  この機能を試す前に、下のコマンドを使用して、登録状態が **Registered** (登録) であることを確認します。これには数分程度かかります。

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


[提供されているリンク](https://aka.ms/diskencryptionupdates)を使用して Azure portal にサインインします。

> [!IMPORTANT]
> [提供されたリンク](https://aka.ms/diskencryptionupdates)を使用して Azure portal にアクセスする必要があります。 現時点では、このリンクを使用しないと、ホストでの暗号化がパブリックの Azure portal に表示されません。

## <a name="deploy-a-vm-with-platform-managed-keys"></a>プラットフォーム マネージド キーを使用して VM をデプロイする

1. [Azure portal](https://aka.ms/diskencryptionupdates) にサインインします。
1. 「**Virtual Machines**」で検索し、 **[+ 追加]** を選択して、VM を作成します。
1. 新しい仮想マシンを作成し、適切なリージョンとサポートされている VM サイズを選択します。
1. 必要に応じて **[基本]** ペインで他の値を入力した後、 **[ディスク]** ペインに進みます。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="[仮想マシンの作成] の [基本] ペインのスクリーンショット、リージョンと VM サイズが強調されています。":::

1. **[ディスク]** ペインで **[ホストでの暗号化]** を選択します。
1. 必要に応じて、残りの選択を行います。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/host-based-encryption-platform-keys.png" alt-text="[仮想マシンの作成] の [ディスク] ペインのスクリーンショット、[ホストでの暗号化] が強調されています。":::

1. VM のデプロイ プロセスを完了し、ご使用の環境に合った選択を行います。

ホストでの暗号化を有効にした VM をデプロイしたので、ディスクのキャッシュはプラットフォーム マネージド キーを使用して暗号化されます。

## <a name="deploy-a-vm-with-customer-managed-keys"></a>カスタマー マネージド キーを使用して VM をデプロイする

あるいは、カスタマー マネージド キーを使用してディスク キャッシュを暗号化することもできます。

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault とディスク暗号化セットを作成する

機能が有効になったら、Azure Key Vault とディスク暗号化セットを設定する必要があります (まだ行っていない場合)。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM をデプロイする

これで Azure Key Vault とディスク暗号化セットが設定できたので、VM をデプロイすれば、ホストでの暗号化を実行できます。

1. [Azure portal](https://aka.ms/diskencryptionupdates) にサインインします。
1. 「**Virtual Machines**」で検索し、 **[+ 追加]** を選択して、VM を作成します。
1. 新しい仮想マシンを作成し、適切なリージョンとサポートされている VM サイズを選択します。
1. 必要に応じて **[基本]** ペインで他の値を入力した後、 **[ディスク]** ペインに進みます。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="[仮想マシンの作成] の [基本] ペインのスクリーンショット、リージョンと VM サイズが強調されています。":::

1. **[ディスク]** ペインの **[SSE encryption type]\(SSE 暗号化の種類\)** で **[Encryption at-rest for customer-managed key]\(カスタマー マネージド キーを使用した保存時の暗号化\)** を選択してから、ディスク暗号化セットを選択します。
1. **[ホストでの暗号化]** を選択します。
1. 必要に応じて、残りの選択を行います。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-host-based-encryption-customer-managed-keys.png" alt-text="[仮想マシンの作成] の [ディスク] ブレードのスクリーンショット、[ホストでの暗号化] が強調され、カスタマー マネージド キーが選択されています。":::

1. VM のデプロイ プロセスを完了し、ご使用の環境に合った選択を行います。

これで、ホストでの暗号化を有効にした VM をデプロイしました。

## <a name="disable-host-based-encryption"></a>ホスト ベースの暗号化を無効にする

最初に、VM の割り当てが解除されていることを確認します。VM の割り当てが解除されていない場合は、ホストでの暗号化を無効にできません。

1. VM で **[ディスク]** を選択し、 **[追加の設定]** を選択します。

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-host-based-encryption-additional-settings.png" alt-text="VM の [ディスク] ペインのスクリーンショット、[追加の設定] が強調されています。":::

1. **[ホストでの暗号化]** で **[いいえ]** を選択して、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
