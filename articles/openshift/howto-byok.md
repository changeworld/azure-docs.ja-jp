---
title: Azure Red Hat OpenShift (ARO) でカスタマー マネージド キー (CMK) を使用して OS ディスクを暗号化する
description: Azure Red Hat OpenShift (ARO) でカスタマー マネージド キー (CMK) を使用して OS ディスクを暗号化する
author: sayjadha
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: 暗号化, BYOK, ARO, デプロイ, OpenShift, Red Hat
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: b4df39040ac3cfbab2ecfa8cb24c72ff19ee5bdc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048237"
---
# <a name="encrypt-os-disks-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Azure Red Hat OpenShift (ARO) でカスタマー マネージド キー (CMK) を使用して OS ディスクを暗号化する (プレビュー)

既定では、Azure Red Hat OpenShift クラスター内の仮想マシンの OS ディスクは、Microsoft Azure によって管理される自動生成キーで暗号化されます。 セキュリティを強化するため、お客様は ARO クラスターをデプロイするときに、自己管理型キーを使用して OS ディスクを暗号化できます。 この機能を使用すると、カスタマー マネージド キーで機密データを暗号化することにより、いっそう詳細に制御できます。

カスタマー マネージド キーを使用して作成されたクラスターの既定のストレージ クラスは、そのキーで有効になります。 そのため、OS ディスクとデータ ディスクの両方が、これらのキーによって暗号化されます。 カスタマー マネージド キーは、Azure Key Vault に格納されます。 Azure Key Vault を使用してキーを作成および保守する方法の詳細については、Microsoft Azure ドキュメントの [Azure Disk Storage のサーバー側暗号化](../key-vault/general/basic-concepts.md)に関する記事を参照してください。

ホスト ベースの暗号化では、ARO エージェント ノードの VM の VM ホストに格納されているデータは保存時に暗号化され、Storage サービスに対してフローが暗号化されます。 つまり、一時ディスクは、プラットフォーム マネージド キーを使用して保存時に暗号化されます。 OS ディスクとデータ ディスクのキャッシュは、ディスクに設定された暗号化のタイプに応じて、プラットフォーム マネージド キーまたはカスタマー マネージド キーのどちらかを使用して保存時に暗号化されます。 既定では、ARO を使用する場合、OS ディスクとデータ ディスクは、プラットフォーム マネージド キーを使用して保存時に暗号化されます。つまり、これらのディスクのキャッシュも、プラットフォーム マネージド キーを使用して保存時に暗号化されます。 次の暗号化手順に従って、独自のマネージド キーを指定できます。 以後、これらのディスクのキャッシュも、この手順で指定するキーを使用して暗号化されるようになります。

> [!IMPORTANT]
> ARO のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビュー機能は、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル アグリーメントおよび限定保証からは除外されます。 プレビュー機能は、カスタマー サポートによりベストエフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。

## <a name="limitation"></a>制限事項
Azure で Key Vault とディスク暗号化セットを管理するのはお客様の責任です。 キーの管理を怠ると、ARO クラスターが破損します。 VM が動作しなくなるため、ARO クラスター全体の機能が停止します。 Azure Red Hat OpenShift のエンジニアリング チームはキーにアクセスできないため、キーのバックアップ、レプリケート、または取得を行うことはできません。 ディスク暗号化セットを使用して暗号化キーを管理する方法の詳細については、Microsoft Azure ドキュメントの [Azure Disk Storage のサーバー側暗号化](../virtual-machines/disk-encryption.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件
* [アクセス許可を確認します](tutorial-create-cluster.md#verify-your-permissions)。 共同作成者とユーザー アクセス管理者のアクセス許可、または所有者のアクセス許可が必要です。
* Azure サブスクリプションが複数ある場合は、リソース プロバイダーを登録します。 登録の詳細については、「[リソース プロバイダーを登録する](tutorial-create-cluster.md#register-the-resource-providers)」を参照してください。

## <a name="install-the-preview-azure-cli-extension"></a>プレビューの Azure CLI 拡張機能をインストールする
Azure コマンド ライン インターフェイス (CLI) をインストールし、それを使用してキー コンテナーを作成します。 Azure CLI を使用すると、対話型のコマンド ライン プロンプトまたはスクリプトを使用して、ターミナルからコマンドを実行できます。
> [!NOTE]
> CLI 拡張機能は、プレビュー機能にのみ必要です。

1. 次の URL をクリックして、Python wheel と CLI 拡張機能の両方をダウンロードしてください。[https://aka.ms/az-aroext-latest.whl](https://aka.ms/az-aroext-latest.whl)
1. 次のコマンドを実行します。
    ```azurecli-interactive
    az extension add --upgrade -s <path to downloaded .whl file>
    ```
1. CLI 拡張機能が使用されていることを確認します。
    ```azurecli-interactive
    az extension list
    [
      {
        "experimental": false,
        "extensionType": "whl",
        "name": "aro",
        "path": "<path may differ depending on system>",
        "preview": true,
        "version": "1.0.1"
      }
    ]
    ```

## <a name="create-a-virtual-network-containing-two-empty-subnets"></a>2 つの空のサブネットを含む仮想ネットワークを作成する
2 つの空のサブネットを含む仮想ネットワークを作成します。 要件を満たす仮想ネットワークが既にある場合は、このステップを省略できます。 仮想ネットワークを作成する手順を確認するには、「[2 つの空のサブネットを含む仮想ネットワークを作成する](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets)」を参照してください。

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault インスタンスを作成する
キーを格納するには、Azure Key Vault のインスタンスを使用する必要があります。 消去保護が有効になっている新しいキー コンテナーを作成します。 次に、独自のカスタム キーを格納するための新しいキーを Key Vault に作成します。
1. 追加の環境アクセス許可を設定します。
    ```
    export KEYVAULT_NAME=$USER-enckv
    export KEYVAULT_KEY_NAME=$USER-key
    export DISK_ENCRYPTION_SET_NAME=$USER-des
    ```
1. キー コンテナーを作成して、Key Vault 内にキーを作成します。
    ```azurecli-interactive
    az keyvault create -n $KEYVAULT_NAME \
                   -g $RESOURCEGROUP \
                   -l $LOCATION \
                   --enable-purge-protection true \
                   --enable-soft-delete true

    az keyvault key create --vault-name $KEYVAULT_NAME \
                           -n $KEYVAULT_KEY_NAME \
                           --protection software

    KEYVAULT_ID=$(az keyvault show --name $KEYVAULT_NAME --query "[id]" -o tsv)

    KEYVAULT_KEY_URL=$(az keyvault key show --vault-name $KEYVAULT_NAME \
                                            --name $KEYVAULT_KEY_NAME \
                                            --query "[key.kid]" -o tsv)
    ```

## <a name="create-an-azure-disk-encryption-set"></a>Azure ディスク暗号化セットを作成する
Azure Disk Encryption セットは、ARO クラスターのディスクの参照ポイントとして使用されます。 それは前のステップで作成した Azure Key Vault に接続され、その場所からカスタマー マネージド キーをプルします。
```azurecli-interactive
az disk-encryption-set create -n $DISK_ENCRYPTION_SET_NAME \
                              -l $LOCATION \
                              -g $RESOURCEGROUP \
                              --source-vault $KEYVAULT_ID \
                              --key-url $KEYVAULT_KEY_URL

DES_ID=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME -g $RESOURCEGROUP --query 'id' -o tsv)

DES_IDENTITY=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME \
                                           -g $RESOURCEGROUP \
                                           --query "[identity.principalId]" \
                                           -o tsv)
```

## <a name="grant-permissions-for-the-disk-encryption-set-to-access-the-key-vault"></a>Key Vault にアクセスするためのアクセス許可をディスク暗号化セットに付与する
前のステップで作成したディスク暗号化セットを使用し、ディスク暗号化セットに Azure Key Vault にアクセスして使用するアクセス許可を付与します。
```azurecli-interactive
az keyvault set-policy -n $KEYVAULT_NAME \
                       -g $RESOURCEGROUP \
                       --object-id $DES_IDENTITY \
                       --key-permissions wrapkey unwrapkey get
```

## <a name="create-an-aro-cluster"></a>ARO クラスターの作成
カスタマー マネージド キーを使用するための ARO クラスターを作成します。
```azurecli-interactive
az aro create --resource-group $RESOURCEGROUP \
              --name $CLUSTER  \
              --vnet aro-vnet  \
              --master-subnet master-subnet \
              --worker-subnet worker-subnet \
              --disk-encryption-set $DES_ID
```
ARO クラスターを作成すると、すべての VM がカスタマー マネージド暗号化キーで暗号化されます。

キーを正しく構成したことを確認するには、次のコマンドを実行します。
1. クラスターの VM やディスクなどが存在するクラスター リソース グループの名前を取得します。
    ```azurecli-interactive
    CLUSTERRESOURCEGROUP=$(az aro show --resource-group $RESOURCEGROUP --name $CLUSTER --query 'clusterProfile.resourceGroupId' -o tsv | cut -d '/' -f 5)
    ```
2. ディスクに正しいディスク暗号化セットがアタッチされていることを確認します。
    ```azurecli-interactive
    az disk list -g $CLUSTERRESOURCEGROUP --query '[].encryption'
    ```
    出力の `diskEncryptionSetId` フィールドは、ARO クラスターを作成するときに指定したディスク暗号化セットを指し示している必要があります。
