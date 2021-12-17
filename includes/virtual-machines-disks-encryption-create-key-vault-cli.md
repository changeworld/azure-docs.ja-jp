---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2021
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 23b6c4dddd839351e7eff49b6dea4c42fe6452d8
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825035"
---
1. [Azure CLI ](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/reference-index) で Azure アカウントにログインしていることを確認します。

1. Azure Key Vault と暗号化キーのインスタンスを作成します。

    Key Vault インスタンスを作成する場合、消去保護を有効にする必要があります。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。

    > [!IMPORTANT]
    > リージョンにキャメル ケースを使用しないでください。キャメル ケースを使用すると、Azure portal のリソースに追加のディスクを割り当てるときに問題が発生することがあります。

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName \
    -g $rgName \
    -l $location \
    --enable-purge-protection true 
                       
    az keyvault key create --vault-name $keyVaultName \
    -n $keyName \
    --protection software
    ```

1.    DiskEncryptionSet のインスタンスを作成します。 enable-auto-key-rotation を true に設定すると、キーの自動回転が有効になります。 自動ローテーションを有効にすると、ディスク暗号化セットを参照するすべてのマネージド ディスク、スナップショット、およびイメージがシステムによって自動的に更新され、1 時間以内に新しいバージョンのキーが使用されます。
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName 
        -l $location \
        -g $rgName \
        --source-vault $keyVaultId \
        --key-url $keyVaultKeyUrl \
        --enable-auto-key-rotation false
        ```

1.    DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。 

        > [!NOTE]
        > Azure がお使いの Azure Active Directory にご自分の DiskEncryptionSet の ID を作成するのには数分かかる場合があります。 次のコマンドを実行しているときに "Active Directory オブジェクトが見つかりません" のようなエラーが表示された場合は、数分待ってから再試行してください。

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName \
        -g $rgName \
        --object-id $desIdentity \
        --key-permissions wrapkey unwrapkey get
        ```
