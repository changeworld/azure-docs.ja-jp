---
title: Azure Key Vault と Kubernetes の統合
description: このチュートリアルでは、シークレット ストア CSI (コンテナー ストレージ インターフェイス) ドライバーを使用して Azure Key Vault にアクセスしてシークレットを取得し、Kubernetes ポッドにマウントします。
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636936"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>チュートリアル:Kubernetes でシークレット ストア CSI ドライバー用に Azure Key Vault プロバイダーを構成して実行する

このチュートリアルでは、シークレット ストア CSI (コンテナー ストレージ インターフェイス) ドライバーを使用して Azure Key Vault にアクセスしてシークレットを取得し、Kubernetes ポッドにマウントします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サービス プリンシパルの作成
> * Azure Kubernetes Service クラスターをデプロイする
> * Helm とシークレット ストア CSI ドライバーをインストールする
> * Azure キー コンテナーを作成してシークレットを設定する
> * 独自の SecretProviderClass オブジェクトを作成する
> * Key Vault からマウントされたシークレットを使用してポッドをデプロイする

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このチュートリアルを開始する前に、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) をインストールします。

## <a name="create-a-service-principal-or-use-managed-identities"></a>サービス プリンシパルを作成するかマネージド ID を使用する

マネージド ID の使用を予定している場合は、次のセクションに進むことができます。

Azure キー コンテナーからアクセスできるリソースを制御するには、サービス プリンシパルを作成します。 このサービス プリンシパルのアクセスは、それに割り当てられたロールによって制限されます。 この機能を使用すると、サービス プリンシパルでシークレットを管理する方法を制御できます。 次の例では、サービス プリンシパルの名前が **contosoServicePrincipal** となります。

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
この操作では、一連のキーと値のペアが返されます。

![Image](../media/kubernetes-key-vault-1.png)

**appID** と **password** を書き留めておきます。 これらの資格情報は後で必要になります。



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Azure CLI を使用して Azure Kubernetes Service クラスターをデプロイする

Azure Cloud Shell を使用する必要はありません。Azure CLI がインストールされているコマンド プロンプト (ターミナル) によって実行されます。 

この[ガイド](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)に従い、「**リソース グループを作成する**」、「**AKS クラスターの作成**」、「**クラスターに接続する**」セクションを完了します。

**注:** サービス プリンシパルではなくポッド ID を使用する予定の場合は、 次に示すように、Kubernetes クラスターの作成時に必ず有効にしてください。

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [PATH 環境変数](https://www.java.com/en/download/help/path.xml)を、ダウンロードされた "kubectl.exe" ファイルに設定します。
1. 以下のコマンドを使用して、Kubernetes のバージョンを確認します。 このコマンドにより、クライアントとサーバーのバージョンが出力されます。 クライアントのバージョンは、インストールした "kubectl.exe" ですが、サーバーのバージョンは、クラスターが実行されている Azure Kubernetes Service です。
    ```azurecli
    kubectl version
    ```
1. Kubernetes のバージョンが **v1.16.0** 以上であることを確認します。 このコマンドにより、Kubernetes クラスターとノード プールの両方がアップグレードされます。 実行には数分かかる場合があります。 この例では、リソース グループは **contosoResourceGroup**、Kubernetes クラスターは **contosoAKSCluster** です。
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. 以下のコマンドを使用して、作成した AKS クラスターのメタデータを表示します。 **principalId**、**clientId**、**subscriptionId**、**nodeResourceGroup** を書き留めておきます。
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    これは、両方のパラメーターが強調表示されている出力です。
    
    ![画像](../media/kubernetes-key-vault-5.png) ![画像](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Helm とシークレット ストア CSI ドライバーをインストールする

シークレット ストア CSI ドライバーをインストールするには、[Helm](https://helm.sh/docs/intro/install/) をインストールする必要があります。

[シークレット ストア CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) ドライバー インターフェイスを使用すると、Azure Key Vault インスタンスに格納されているシークレット コンテンツを取得し、このドライバー インターフェイスを使用してこれらのシークレット コンテンツを Kubernetes ポッドにマウントすることができます。

1. Helm のバージョンをチェックし、v3 以上であることを確認します。
    ```azurecli
    helm version
    ```
1. シークレット ストア CSI ドライバーとそのドライバー用の Azure Key Vault プロバイダーをインストールします。
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Azure キー コンテナーを作成してシークレットを設定する

この[ガイド](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)に従って、独自のキー コンテナーを作成してシークレットを設定します。

**注:** Azure Cloud Shell を使用したり、新しいリソース グループを作成したりする必要はありません。 Kubernetes クラスター用に以前作成したリソース グループを使用すれば問題ありません。

## <a name="create-your-own-secretproviderclass-object"></a>独自の SecretProviderClass オブジェクトを作成する

独自のカスタム SecretProviderClass オブジェクトを作成して、シークレット ストア CSI ドライバーにプロバイダー固有のパラメーターを指定するために用意されたこの[テンプレート](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml)を使用します。 このオブジェクトにより、自分のキー コンテナーへのアクセス権が ID に提供されます。

提供されているサンプルの SecretProviderClass YAML ファイルを使用します。 不足しているパラメーターに入力します。 次のパラメーターが必要です。

1.  **userAssignedIdentityID:** サービス プリンシパルのクライアント ID
1.  **keyvaultName:** キー コンテナーの名前。
1.  **objects:** このオブジェクトには、マウントするシークレットの内容すべてを含めます
    1.  **objectName:** シークレット コンテンツの名前
    1.  **objectType:** オブジェクトの種類 (シークレット、キー、証明書)
1.  **resourceGroup:** リソース グループの名前
1.  **subscriptionId:** キー コンテナーのサブスクリプション ID
1.  **tenantID:** キー コンテナーのテナント ID (つまり、ディレクトリ ID)

更新されたテンプレートを以下に示します。これを .yaml ファイルとしてダウンロードし、対応する必須フィールドに情報を入力します。 この例では、キー コンテナーは **contosoKeyVault5** で、2 つのシークレット **secret1** と **secret2** を持ちます。

**注:** マネージド ID を使用している場合、**usePodIdentity** フィールドは **true** にして、**userAssignedIdentityID** フィールドは単に引用符 **""** のままにしておく必要があります。 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
"az keyvault show --name contosoKeyVault5" に対するコンソール出力は次のとおりです。関連するメタデータが強調表示されています。

![Image](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>サービス プリンシパルを割り当てるかマネージド ID を使用する

### <a name="using-service-principal"></a>サービス プリンシパルを使用する

サービス プリンシパルを使用する場合、 自分のキー コンテナーにアクセスしてシークレットを取得するためのアクセス許可をサービス プリンシパルに付与する必要があります。 **"閲覧者"** ロールを割り当て、キー コンテナーからシークレットを **"取得する"** ためのアクセス許可をサービス プリンシパルに付与するには、以下の手順を実行します。

1. 既存のキー コンテナーにサービス プリンシパルを割り当てます。 **$AZURE_CLIENT_ID** パラメーターは、サービス プリンシパルを作成した後に書き留めた **appId** です。
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    このコマンドの出力を以下に示します。 

    ![Image](../media/kubernetes-key-vault-3.png)

1. シークレットを取得するためのアクセス許可をサービス プリンシパルに付与します。
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. これで、キー コンテナーからシークレットを読み取るためのアクセス許可が付与されるようサービス プリンシパルが構成されました。 **$AZURE_CLIENT_SECRET** は、サービス プリンシパルの**パスワード**です。 シークレット ストア CSI ドライバーからアクセスできる Kubernetes シークレットとして、サービス プリンシパルの資格情報を追加します。
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**注:** Kubernetes ポッドをデプロイしたときに、無効なクライアント シークレット ID に関するエラーが後で発生した場合、 有効期限が切れたかリセットされた古いクライアント シークレット ID を使用している可能性があります。 この問題を解決するには、シークレット "secrets-store-creds" を削除し、現在のクライアント シークレット ID を使用して新しいものを作成してください。 "secrets-store-creds" を削除するには、以下のコマンドを実行します。
```azurecli
kubectl delete secrets secrets-store-creds
```

サービス プリンシパルのクライアント シークレット ID を忘れた場合は、次のコマンドを使用してリセットすることができます。

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>マネージド ID を使用する

マネージド ID を使用する場合は、作成した AKS クラスターに特定のロールを割り当てます。 
1. ユーザー割り当て済みマネージド ID の作成、一覧表示、読み取りを実行するには、AKS クラスターに[マネージド ID 共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor)ロールを割り当てる必要があります。 **$clientId** が Kubernetes クラスターのものであることを確認します。

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. AKS に Azure Active Directory (Azure AD) ID をインストールします。
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Azure AD ID を作成します。 **clientId** と **principalId** を書き留めておきます。
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. キー コンテナー用に作成した Azure AD ID に閲覧者ロールを割り当てます。 その後、その ID に対して、キー コンテナーからシークレットを取得するためのアクセス許可を付与します。 ここでは、作成した Azure ID の **clientId** と **principalId** を使用します。
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Key Vault からマウントされたシークレットを使用してポッドをデプロイする

次のコマンドでは、SecretProviderClass オブジェクトが構成されます。
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>サービス プリンシパルを使用する

サービス プリンシパルを使用する場合、 以下のコマンドにより、SecretProviderClass と構成した secrets-store-creds を使用して Kubernetes ポッドがデプロイされます。 ここでは、[Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) と [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) デプロイ用のテンプレートを示します。
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>マネージド ID を使用する

マネージド ID を使用する場合、 先ほど作成した ID を参照する **AzureIdentity** を自分のクラスター内に作成します。 次に、作成した **AzureIdentity** を参照する **AzureIdentityBinding** を作成します。 以下のテンプレートを使用し、対応するパラメーターに情報を入力して、**podIdentityAndBinding.yaml** として保存します。  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
次のコマンドを実行して、バインドを実行します。

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

次は、実際のポッドのデプロイです。 最後の手順からポッド ID のバインドを使用する、デプロイの YAML ファイルを以下に示します。 このファイルを **podBindingDeployment.yaml** として保存します。

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

次のコマンドを実行してポッドをデプロイします。

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>状態とシークレット コンテンツを確認する 
デプロイしたポッドを表示するには:
```azurecli
kubectl get pods
```

ポッドの状態を確認するには、次のコマンドを使用します。
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Image](../media/kubernetes-key-vault-4.png)

デプロイされたポッドは "実行中" 状態になります。 下部の "Events" セクションでは、左側にあるイベントの種類がすべて、"Normal" として分類されています。
ポッドが実行中であることを確認したら、ポッドにキー コンテナーからシークレットが含まれていることを確認できます。

ポッドに含まれるすべてのシークレットを表示するには:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

特定のシークレットから内容を取得するには:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

表示されたシークレットの内容を確認します。

## <a name="next-steps"></a>次のステップ

キー コンテナーが回復可能であることを確認します。
> [!div class="nextstepaction"]
> [論理的な削除を有効にする](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
