---
title: Azure Key Vault プロバイダーへのアクセス ID を Azure Kubernetes Service (AKS) シークレット用のシークレット ストア CSI ドライバーに提供する
description: シークレット ストア CSI ドライバーの Azure Key Vault プロバイダーを Azure Key Vault (AKV) と統合できるようにするために使用できるさまざまな方法について説明します。
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9e0741cd9f1f90efc4184891401d1d05fc5b5b2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511337"
---
# <a name="provide-an-identity-to-access-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Azure Key Vault プロバイダーにアクセスするための ID をシークレット ストア CSI ドライバーに提供する

Azure Kubernetes Service (AKS) のシークレット ストア CSI ドライバーには、Azure Key Vault (AKV) に ID ベースでアクセスするためのさまざまな方法が用意されています。 この記事では、これらの方法を概説し、これらの方法を使用してクラスターから AKV インスタンスとそのコンテンツにアクセスする方法について説明します。 詳細については、[シークレット ストア CSI ドライバーの使用][csi-secrets-store-driver]に関する記事を参照してください。

## <a name="use-pod-identity"></a>ポッド ID を使用する

Azure Active Directory ポッドマネージド ID では、Kubernetes プリミティブを使用して Azure リソース用マネージド ID と Azure Active Directory (AAD) の ID をポッドに関連付けます。 これらの ID を使用して、シークレット ストア CSI ドライバーに Azure Key Vault プロバイダーへのアクセス権を付与することができます。

### <a name="prerequisites"></a>前提条件

- クラスターで [AAD ポッド ID のアドオン][aad-pod-identity]が有効になっていることを確認します
- Linux ベースのクラスターを使用する必要があります

### <a name="usage"></a>使用

1. [AAD ポッドマネージド ID の使用][aad-pod-identity-create]に関するページの手順に従って、クラスター ID を作成し、これにアクセス許可を割り当てて、ポッド ID を作成します。 新しく作成した ID の `clientId` と `name` をメモしておきます。

2. 次を実行して、新しい ID にアクセス許可を割り当て、AKV インスタンスを読み取って、そのコンテンツを表示できるようにします。

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
```

3. 次の YAML を使用して SecretProviderClass を作成し、AKV インスタンスから取得する `aadpodidbinding`、`tenantId`、およびオブジェクトの値を入力します。

```yml
# This is a SecretProviderClass example using aad-pod-identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-podid
spec:
  provider: azure
  parameters:
    usePodIdentity: "true"               # Set to true for using aad-pod-identity to access keyvault
    keyvaultName: <key-vault-name>       # Set to the name of your Azure Key Vault instance
    cloudName: ""                        # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret             # object types: secret, key or cert
          objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-Id>                # The tenant ID of the Azure Key Vault instance
```

4. クラスターに SecretProviderClass を適用します。

```bash
kubectl apply -f secretproviderclass.yaml
```

5. 次の YAML を使用してポッドを作成し、ID の名前を入力します。

```yml
# This is a sample pod definition for using SecretProviderClass and aad-pod-identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-podid
  labels:
    aadpodidbinding: <name>                   # Set the label value to the name of your pod identity
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-podid"
```

6. ポッドをクラスターに適用します。

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を使用する

1. [AKS クラスターでマネージド ID を有効にする][use-managed-identity]ときに作成されたユーザー割り当てマネージド ID を使用して、AKV インスタンスにアクセスできます。

```azurecli-interactive
az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
```

あるいは、新しいものを作成して、仮想マシン スケール セット、または可用性セット内の各 VM インスタンスに割り当てることもできます。

```azurecli-interactive
az identity create -g <resource-group> -n <identity-name> 
az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
```

2. 次を実行して、ID にアクセス許可を付与し、AKV インスタンスを読み取って、そのコンテンツを表示できるようにします。

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
```

3. 次の YAML を使用して SecretProviderClass を作成し、AKV インスタンスから取得する `userAssignedIdentityID`、`keyvaultName`、`tenantId`、およびオブジェクトの値を入力します。

```yml
# This is a SecretProviderClass example using user-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-user-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"          # Set to true for using managed identity
    userAssignedIdentityID: <client-id>   # Set the clientID of the user-assigned managed identity to use
    keyvaultName: <key-vault-name>        # Set to the name of your Azure Key Vault instance
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret              # object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>                 # The tenant ID of the Azure Key Vault instance
```

4. クラスターに SecretProviderClass を適用します。

```bash
kubectl apply -f secretproviderclass.yaml
```

5. 次の YAML を使用してポッドを作成し、ID の名前を入力します。

```yml
# This is a sample pod definition for using SecretProviderClass and user-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-user-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-user-msi"
```

6. ポッドをクラスターに適用します。

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用する

### <a name="prerequisites"></a>前提条件

- クラスターの VM またはスケール セットで、[システム割り当てマネージド ID を有効にします][enable-system-assigned-identity]

### <a name="usage"></a>使用

1. 仮想マシン スケール セットまたは可用性セット ノードに、独自のシステム割り当て ID があることを確認します。

```azurecli-interactive
az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
az vm identity show -g <resource group> -n <vm name> -o yaml
```

この出力に、`type: SystemAssigned` が含まれています。 `principalId` を書き留めておきます。

2. 次を実行して、ID にアクセス許可を割り当て、AKV インスタンスを読み取って、そのコンテンツを表示できるようにします。

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
```

3. 次の YAML を使用して SecretProviderClass を作成し、AKV インスタンスから取得する `keyvaultName`、`tenantId`、およびオブジェクトの値を入力します。

```yml
# This is a SecretProviderClass example using system-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-system-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"    # Set to true for using managed identity
    userAssignedIdentityID: ""      # If empty, then defaults to use the system assigned identity on the VM
    keyvaultName: <key-vault-name>
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>           # The tenant ID of the Azure Key Vault instance
```

4. クラスターに SecretProviderClass を適用します。

```bash
kubectl apply -f secretproviderclass.yaml
```

5. 次の YAML を使用してポッドを作成し、ID の名前を入力します。

```yml
# This is a sample pod definition for using SecretProviderClass and system-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-system-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-system-msi"
```

## <a name="next-steps"></a>次の手順

ポッドの YAML に指定されたボリューム パスに[シークレットがマウントされていることを検証します][validate-secrets]。

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
