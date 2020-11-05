---
title: インクルード ファイル
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: ecbafe0d3f39b1bd6f7c494695ea17e067f0c79e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129280"
---
## <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 これを実現するには、オペレーティング システム VHD を一般化する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

### <a name="for-windows"></a>Windows の場合

Windows OS ディスクは、[sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) ツールを使用して一般化されます。 後で OS を更新または再構成する場合は、sysprep を再び実行する必要があります。

> [!WARNING]
> 更新プログラムが自動的に実行される可能性があるため、sysprep を実行した後、デプロイされるまでは VM をオフにしてください。 このシャットダウンで、以降の更新プログラムによるオペレーティング システムまたはインストール済みサービスへのインスタンス固有の変更が行われなくなります。 sysprep の実行に関する詳細については、[VHD を一般化する手順](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)に関する記事を参照してください。

### <a name="for-linux"></a>Linux の場合

次のプロセスでは、Linux VM を一般化して別の VM として再デプロイします。 詳細については、「[仮想マシンまたは VHD のイメージを作成する方法](../../virtual-machines/linux/capture-image.md)」を参照してください。 「キャプチャしたイメージから VM を作成する」というセクションに達したら止めることができます。

1. Azure Linux エージェントを削除します。
    1. SSH クライアントを使って Linux VM に接続します。
    2. SSH ウィンドウで、コマンド `sudo waagent –deprovision+user` を入力します。
    3. 「Y」と入力して続行します (前のコマンドに -force パラメーターを追加すると、この確認手順を省略できます)。
    4. コマンドが完了したら、「 **Exit** 」と入力して SSH クライアントを閉じます。
2. 仮想マシンを停止します。
    1. Azure portal でリソース グループ (RG) を選択し、VM の割り当てを解除します。
    2. これで VM が一般化されたため、この VM ディスクを使用して新しい VM を作成できます。

### <a name="take-a-snapshot-of-the-vm-disk"></a>VM ディスクのスナップショットを作成する

1. [Azure portal](https://ms.portal.azure.com/) にサインインします。
2. 左上の **[リソースの作成]** を選択し、 **[スナップショット]** を検索して選択します。
3. **[スナップショット]** ブレードで、[作成] を選択します。
4. スナップショットの **[名前]** を入力します。
5. 既存のリソース グループを選択するか、新しいものの名前を入力します。
6. **[ソース ディスク]** で、スナップショットを作成するマネージド ディスクを選びます。
7. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスの SSD に保存する必要がある場合を除き、 **[Standard HDD]** を使用します。
8. **［作成］** を選択します

#### <a name="extract-the-vhd"></a>VHD を抽出する

次のスクリプトを使用して、スナップショットをストレージ アカウント内の VHD にエクスポートします。

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $ snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用してスナップショットの SAS URI を生成し、その SAS URI を使用して基になる VHD をストレージ アカウントにコピーします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
| --- | --- |
| az ディスク アクセスの許可 | 基盤となる VHD ファイルをストレージ アカウントにコピーするか、オンプレミスにダウンロードするために使用される、読み取り専用の SAS を生成します。
| az storage blob copy start | BLOB を、あるストレージ アカウントから別のストレージ アカウントに非同期的にコピーします。 `az storage blob show` を使用して、新しい BLOB の状態を確認します。 |
|