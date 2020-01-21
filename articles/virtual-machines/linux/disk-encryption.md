---
title: Azure Managed Disks のサーバー側暗号化 - Azure CLI
description: Azure Storage では、保存時に暗号化してデータを保護してから、ストレージ クラスターに保存します。 マネージド ディスクの暗号化には Microsoft のマネージド キーを使用できます。また、カスタマー マネージド キーを使用し、独自のキーを使って暗号化を管理できます。
author: roygara
ms.date: 12/13/2019
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 4495b4489fd98df8dcba47548b64ddec2ad419d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462550"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure Managed Disks のサーバー側暗号化

Azure マネージド ディスクは、データをクラウドに永続化するときに、既定で自動的にデータを暗号化します。 サーバー側の暗号化によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Azure マネージド ディスク内のデータは、利用できる最も強力なブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化され、FIPS 140-2 に準拠しています。   

暗号化はマネージド ディスクのパフォーマンスに影響しません。 暗号化に追加コストはかかりません。

Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:次世代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)」を参照してください。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

プラットフォーム マネージド キーを利用してマネージド ディスクを暗号化することも、独自のキーを使用して暗号化を管理することもできます (パブリック プレビュー)。 独自のキーを使用して暗号化を管理する場合は、マネージド ディスク内のすべてのデータの暗号化と暗号化解除に使用する*カスタマー マネージド キー*を指定できます。 

以降のセクションでは、キー管理の各オプションについてさらに詳しく説明します。

## <a name="platform-managed-keys"></a>プラットフォーム マネージド キー

既定では、マネージド ディスクはプラットフォーム マネージド暗号化キーを使用します。 2017 年 6 月 10 日をもって、新しいすべてのマネージド ディスク、スナップショット、イメージ、および既存のマネージド ディスクに書き込まれる新しいデータは、プラットフォーム マネージド キーで保存時に自動的に暗号化されます。 

## <a name="customer-managed-keys-public-preview"></a>カスタマー マネージド キー (パブリック プレビュー)

ユーザー独自のキーを使用して、各マネージド ディスクのレベルで暗号化を管理できます。 カスタマー マネージド キーを使用したサーバー側でのマネージド ディスクの暗号化により、Azure Key Vault との統合されたエクスペリエンスが提供されます。 [ご使用の RSA キー](../../key-vault/key-vault-hsm-protected-keys.md)を Key Vault にインポートするか、Azure Key Vault で新しい RSA キーを生成することができます。 Azure マネージド ディスクは[エンベロープ暗号化](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)を使用して、暗号化と暗号化解除を完全に透過的な方法で処理します。 これは、[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 ベースのデータ暗号化キー (DEK) を使用してデータを暗号化します。このキーは、ご使用のキーを使用して保護されます。 DEK の暗号化と暗号化解除にキーを使用するための、Key Vault 内のマネージド ディスクへのアクセス権の付与を行う必要があります。 これにより、データとキーを完全に制御できます。 任意の時点で、キーを無効にしたり、マネージド ディスクへのアクセスを取り消したりすることができます。 また、Azure Key Vault 監視で暗号化キーの使用状況を監査して、マネージド ディスクまたは他の信頼された Azure サービスのみがキーにアクセスしていることを確認することもできます。

次の図は、マネージド ディスクで Azure Active Directory と Azure Key Vault を使用して、カスタマー マネージド キーを使って要求を行う方法を示しています。

![マネージド ディスクのカスタマー マネージド キーのワークフロー](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


次の一覧では、図についてさらに詳しく説明します。

1. Azure Key Vault の管理者が Key Vault リソースを作成します。
1. Key Vault 管理者は、RSA キーを Key Vault にインポートするか、Key Vault 内で新しい RSA キーを生成します。
1. この管理者は、Azure Key Vault ID とキー URL を指定して、ディスク暗号化セット リソースのインスタンスを作成します。 ディスク暗号化セットは、マネージド ディスクのキー管理を簡略化するために導入された新しいリソースです。 
1. ディスク暗号化セットを作成すると、[システムによって割り当てられたマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) が Azure Active Directory (AD) に作成され、ディスク暗号化セットに関連付けられます。 
1. Azure Key Vault の管理者はその後、Key Vault 内での操作を実行するためのアクセス許可をマネージド ID に付与します。
1. VM ユーザーは、ディスク暗号化セットにディスクを関連付けることによってディスクを作成します。 また、VM ユーザーは、既存のリソースに対するカスタマー マネージド キーを使用したサーバー側の暗号化を、ディスク暗号化セットへのリソースの関連付けによって有効にすることもできます。 
1. マネージド ディスクは、マネージド ID を使用して Azure Key Vault に要求を送信します。
1. データの読み取りまたは書き込みを行うために、マネージド ディスクは Azure Key Vault に要求を送信して、データの暗号化と暗号化解除を実行するためにデータ暗号化キーを暗号化 (ラップ) および暗号化解除 (ラップ解除) します。 

カスタマー マネージド キーへのアクセスを取り消すには、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) および [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関する記事をご覧ください。 アクセスを取り消すと、Azure Storage が暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全データへのアクセスが事実上ブロックされます。

### <a name="supported-scenarios-and-restrictions"></a>サポートされるシナリオと制限

プレビュー期間中は次のシナリオのみがサポートされています。

- Azure Marketplace イメージから仮想マシン (VM) を作成し、カスタマー マネージド キーを使用してサーバー側の暗号化で OS ディスクを暗号化します。
- サーバー側の暗号化とカスタマー マネージド キーで暗号化されたカスタム イメージを作成します。
- カスタム イメージから VM を作成し、サーバー側の暗号化とカスタマー マネージド キーを使用して OS ディスクを暗号化します。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたデータ ディスクを作成します。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたスナップショットを作成します。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化された仮想マシン スケール セットを作成します。

プレビューには、次の制限もあります。

- 米国中西部でのみご利用いただけます。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたカスタム イメージから作成されたディスクは、同じカスタマー マネージド キーを使用して暗号化する必要があり、同じサブスクリプション内に存在する必要があります。
- サーバー側の暗号化とカスタマー マネージド キーで暗号化されたディスクから作成されたスナップショットは、同じカスタマー マネージド キーを使用して暗号化する必要があります。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたカスタム イメージは、共有イメージ ギャラリーでは使用できません。
- Key Vault は、カスタマー マネージド キーと同じサブスクリプションおよびリージョン内にある必要があります。
- カスタマー マネージド キーで暗号化されたディスク、スナップショット、およびイメージは、別のサブスクリプションに移動できません。

### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault と DiskEncryptionSet の設定

1.  Azure Key Vault と暗号化キーのインスタンスを作成します。

    Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。

    ```azurecli
    subscriptionId = <yourSubscriptionIDHere>
    rgName = <yourResourceGroupNameHere>
    location = <yourDesiredLocationHere>
    keyVaultName = <yourKeyVaultNameHere>
    keyName = <yourKeyNameHere>
    diskEncryptionSetName = <yourDiskEncryptionSetNameHere>
    diskName = <yourDiskNameHere>

    az account set -subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.  DiskEncryptionSet のインスタンスを作成します。 
    
    ```azurecli
    keyVaultId = $(az keyvault show --name $keyVaultName --query [id] -o tsv)

    keyVaultKeyUrl = $(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

    az group deployment create -g $rgName --template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" --parameters "diskEncryptionSetName = $diskEncryptionSetName" "keyVaultId = $keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
    ```

1.  DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。

    ```azurecli
    desIdentity=$(az ad sp list --display-name $diskEncryptionSetName --query[].[objectId] -o tsv)

    az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get

    az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace イメージを使用して VM を作成し、カスタマー マネージド キーで OS とデータ ディスクを暗号化する

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
region="westcentralus"
password="<yourVMLocalAdminPassword>"
vmSize="Standard_DS3_V2"
diskEncryptionSetName="<yourDiskEncryptionSetName>"
templateURI="https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json"

diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

az group deployment create -g $rgName --template-uri $templateURI --parameters "virtualMachineName=$vmName" "adminPassword=$password" "vmSize=$vmSize" "diskEncryptionSetId=$diskEncryptionSetId" "region=$region"

```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>カスタマー マネージド キーを使用したサーバー側の暗号化で暗号化された空のディスクを作成し、VM に接続する

```azurecli
vmName="<yourVMName>"
rgName="<yourResourceGroupName>"
diskName="<yourDiskName>"
diskSkuName="Premium_LRS"
diskSizeinGiB="30"
region="westcentralus"
diskLUN=2
diskEncryptionSetName="<yourDiskEncryptionSetName>"
templateURI="https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json"

diskEncryptionSetId=$(az resource show -n $diskEncryptionSetName -g ssecmktesting --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)


az group deployment create -g $rgName --template-uri $templateURI --parameters  "diskName=$diskName" "diskSkuName=$diskSkuName" "dataDiskSizeInGb=$diskSizeinGiB" "diskEncryptionSetId=$diskEncryptionSetId" "region=$region"

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId

```

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>サーバー側の暗号化と Azure ディスク暗号化の比較

[仮想マシンと仮想マシン スケール セットの Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) では、Windows の [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、ゲスト VM 内のカスタマー マネージド キーを使用してマネージド ディスクを暗号化します。  カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。

## <a name="next-steps"></a>次のステップ

- [カスタマー マネージド キーで暗号化されたディスクを作成するための Azure Resource Manager テンプレートを探索する](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault とは](../../key-vault/key-vault-overview.md)
