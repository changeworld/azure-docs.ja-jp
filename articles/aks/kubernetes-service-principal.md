---
title: Azure Kubernetes Service (AKS) 用のサービス プリンシパル
description: Azure Kubernetes Service (AKS) のクラスター用の Azure Active Directory サービス プリンシパルを作成して管理する
services: container-service
ms.topic: conceptual
ms.date: 04/22/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c2ad9c6eae22db2d940942c0ec15c6b1116a54cd
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353012"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのサービス プリンシパル

AKS クラスターには、Azure API での対話に [Azure Active Directory (AD) サービス プリンシパル][aad-service-principal]または[マネージド ID](use-managed-identity.md) が必要です。 サービス プリンシパルまたはマネージド ID は、Azure のロード バランサーや Azure Container Registry (ACR) などのその他の Azure リソースを動的に作成および管理するために必要です。

この記事では、AKS クラスター用のサービス プリンシパルを作成して使用する方法を示します。

## <a name="before-you-begin"></a>開始する前に

Azure AD サービス プリンシパルを作成するには、アプリケーションを Azure AD テナントに登録し、そのアプリケーションをサブスクリプション内のロールに割り当てるためのアクセス許可が必要です。 必要なアクセス許可がない場合は、必要なアクセス許可を割り当てるよう Azure AD またはサブスクリプションの管理者に依頼するか、AKS クラスターで使用するサービス プリンシパルを事前に作成する必要が生じることがあります。

別の Azure AD テナントのサービス プリンシパルを使用している場合は、クラスターのデプロイ時に使用できるアクセス許可について追加の考慮事項があります。 ディレクトリ情報の読み取りと書き込みを行うために適切なアクセス許可がない可能性があります。 詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは][azure-ad-permissions]」を参照してください。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell バージョン 5.0.0 以降がインストールされていることも必要です。 バージョンを確認するには、`Get-InstalledModule -Name Az` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure Az PowerShell モジュールをインストールする][install-the-azure-az-powershell-module]」を参照してください。

---

## <a name="automatically-create-and-use-a-service-principal"></a>サービス プリンシパルを自動的に作成して使用する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal で、または [az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成すると、Azure によってマネージド ID が作成されます。

次の Azure CLI の例では、サービス プリンシパルは指定されていません。 このシナリオでは、Azure CLI によって、AKS クラスター用のマネージド ID が作成されます。 

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure portal で、または [New-AzAksCluster][new-azakscluster] コマンドを使用して AKS クラスターを作成するときに、Azure から新しいマネージド ID を生成することができます。

次の Azure PowerShell の例では、サービス プリンシパルは指定されていません。 このシナリオでは、Azure PowerShell によって、AKS クラスター用のマネージド ID が作成されます。 

```azurepowershell-interactive
New-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

> [!NOTE]
> エラー「Service principal clientID: 00000000-0000-0000-0000-000000000000 not found in Active Directory tenant 00000000-0000-0000-0000-000000000000」については、「[その他の注意点](#additional-considerations)」を参照して `acsServicePrincipal.json` ファイルを削除してください。

---
## <a name="manually-create-a-service-principal"></a>手動でサービス プリンシパルを作成する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して手動でサービス プリンシパルを作成するには、[az ad sp create-for-rbac][az-ad-sp-create] コマンドを使用します。 次の例では、`--skip-assignment` パラメーターによって、追加の既定の割り当てが行われないようにしています。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

出力は次の例のようになります。 使っている `appId` と `password` をメモします。 これらの値は、次のセクションで AKS クラスターを作成するときに使用します。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell でサービス プリンシパルを手動で作成するには、[New-AzADServicePrincipal][new-azadserviceprincipal] コマンドを使用します。 次の例では、`-SkipAssignment` パラメーターによって、追加の既定の割り当てが行われないようにしています。

```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName myAKSClusterServicePrincipal -SkipAssignment -OutVariable sp
```

出力は次の例のようになります。 これらの値はまた、次のセクションで AKS クラスターを作成するときに使用される変数にも格納されます。

```Output
Secret                : System.Security.SecureString
ServicePrincipalNames : {559513bd-0c19-4c1a-87cd-851a26afd5fc, http://myAKSClusterServicePrincipal}
ApplicationId         : 559513bd-0c19-4c1a-87cd-851a26afd5fc
ObjectType            : ServicePrincipal
DisplayName           : myAKSClusterServicePrincipal
Id                    : 559513bd-0c19-4c1a-87cd-851a26afd5fc
Type                  :
```

**Secret** のセキュリティで保護された文字列に格納されている値の暗号化を解除するには、次の例を使用します。

```azurepowershell-interactive
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
[System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

詳細については、「[Azure PowerShell で Azure サービス プリンシパルを作成する][create-an-azure-service-principal-with-azure-powershell]」を参照してください。

---

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>AKS クラスター用のサービス プリンシパルを指定する

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときに既存のサービス プリンシパルを使用するには、`--service-principal` パラメーターと `--client-secret` パラメーターを使用して、[az ad sp create-for-rbac][az-ad-sp-create] コマンドの出力の `appId` と `password` を指定します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> カスタマイズされたシークレットがある既存のサービス プリンシパルを使用する場合は、そのシークレットの長さが 190 バイトを超えていないことを確認します。

Azure portal を使用して AKS クラスターをデプロイする場合は、 **[Create Kubernetes cluster]\(Kubernetes クラスターの作成)** ダイアログ ボックスの *[Authentication]\(認証)* ページで、 **[Configure service principal]\(サービス プリンシパルの構成)** を選択します。 **[既存のものを使用]** を選択し、以下の値を指定します。

- **[Service principal client ID]\(サービス プリンシパルのクライアント ID)** は、実際の *appId* です
- **[Service principal client secret]\(サービス プリンシパルのクライアント シークレット)** は、*password* の値です

![Azure Vote にブラウザーでアクセスしたところ](media/kubernetes-service-principal/portal-configure-service-principal.png)

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS クラスターを作成するときに既存のサービス プリンシパルを使用するには、次の例に示すように、サービス プリンシパル `ApplicationId` と `Secret` を **PSCredential** オブジェクトに変換する必要があります。

```azurepowershell-interactive
$Cred = New-Object -TypeName System.Management.Automation.PSCredential ($sp.ApplicationId, $sp.Secret)
```

`New-AzAksCluster` コマンドを実行するときは、`ServicePrincipalIdAndSecret` パラメーターを指定し、前に作成された **PSCredential** オブジェクトをその値として使用します。

```azurepowershell-interactive
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -ServicePrincipalIdAndSecret $Cred
```

> [!NOTE]
> カスタマイズされたシークレットがある既存のサービス プリンシパルを使用する場合は、そのシークレットの長さが 190 バイトを超えていないことを確認します。

Azure portal を使用して AKS クラスターをデプロイする場合は、 **[Create Kubernetes cluster]\(Kubernetes クラスターの作成)** ダイアログ ボックスの *[Authentication]\(認証)* ページで、 **[Configure service principal]\(サービス プリンシパルの構成)** を選択します。 **[既存のものを使用]** を選択し、以下の値を指定します。

- **[サービス プリンシパルのクライアント ID]** は、作成した *ApplicationId* です。
- **[サービス プリンシパルのクライアント シークレット]** は、暗号化解除された *Secret* 値です。

![Azure Vote にブラウザーでアクセスしたところ](media/kubernetes-service-principal/portal-configure-service-principal.png)

---

## <a name="delegate-access-to-other-azure-resources"></a>他の Azure リソースへのアクセスを委任する

AKS クラスターのサービス プリンシパルは、他のリソースへのアクセスに使用することができます。 たとえば、AKS クラスタを既存の Azure 仮想ネットワーク サブネットに展開したり、Azure Container Registry (ACR) に接続したりする場合、それらのリソースへのアクセスをサービス プリンシパルに委任する必要があります。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

アクセス許可を委任するには、[az role assignment create][az-role-assignment-create] コマンドを使用してロールの割り当てを作成します。 リソース グループ、仮想ネットワーク リソースなど、特定のスコープに `appId` を割り当てます。 さらに、そのサービス プリンシパルが対象のリソースに対して持つアクセス許可がロールによって定義されます。次の例をご覧ください。

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

リソースの `--scope` には、 */subscriptions/\<guid\>/resourceGroups/myResourceGroup* や */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet* など、完全なリソース ID を指定する必要があります。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

アクセス許可を委任するには、[New-AzRoleAssignment][new-azroleassignment] コマンドを使用してロールの割り当てを作成します。 リソース グループ、仮想ネットワーク リソースなど、特定のスコープに `ApplicationId` を割り当てます。 さらに、そのサービス プリンシパルが対象のリソースに対して持つアクセス許可がロールによって定義されます。次の例をご覧ください。

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId <ApplicationId> -Scope <resourceScope> -RoleDefinitionName Contributor
```

リソースの `Scope` には、 */subscriptions/\<guid\>/resourceGroups/myResourceGroup* や */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet* など、完全なリソース ID を指定する必要があります。

---

> [!NOTE]
> ノード リソース グループから共同作成者ロールの割り当てを削除した場合、以下の操作が失敗する可能性があります。
> システム マネージド ID を使用しているクラスターへのアクセス許可付与の入力には、最大で 60 分かかる場合があります。

以降の各セクションでは、一般的に必要となる委任について詳しく取り上げます。

### <a name="azure-container-registry"></a>Azure Container Registry

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Container Registry (ACR) をコンテナーのイメージ ストアとして使用する場合、AKS クラスターがイメージを読み取ってプルするためのアクセス許可を、サービス プリンシパルに与える必要があります。 現在は、[az aks create][az-aks-create] または [az aks update][az-aks-update] コマンドを使用してレジストリと統合し、サービス プリンシパルに適切なロールを割り当てる構成をお勧めします。 詳細な手順については、「[Azure Kubernetes Service から Azure Container Registry の認証を受ける][aks-to-acr]」を参照してください。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Container Registry (ACR) をコンテナーのイメージ ストアとして使用する場合、AKS クラスターがイメージを読み取ってプルするためのアクセス許可を、サービス プリンシパルに与える必要があります。 現在、推奨される構成は、[New-AzAksCluster][new-azakscluster] または [Set-AzAksCluster][set-azakscluster] コマンドを使用してレジストリと統合し、サービス プリンシパルに適切なロールを割り当てる方法です。 詳細な手順については、「[Azure Kubernetes Service から Azure Container Registry の認証を受ける][aks-to-acr]」を参照してください。

---

### <a name="networking"></a>ネットワーク

仮想ネットワークとサブネットまたはパブリック IP アドレスが別のリソース グループに存在する高度なネットワークを使用することも考えられます。 仮想ネットワーク内のサブネットに[ネットワーク共同作成者][rbac-network-contributor]の組み込みロールを割り当てます。 または、そのリソース グループ内のネットワーク リソースにアクセスするためのアクセス許可を持つ[カスタム ロール][rbac-custom-role]を作成することもできます。 詳細については、「[AKS サービスのアクセス許可][aks-permissions]」を参照してください。

### <a name="storage"></a>ストレージ

別のリソース グループにある既存のディスク リソースにアクセスしなければならない場合があります。 ロールの一連のアクセス許可として、次のいずれかを割り当てます。

- [カスタム ロール][rbac-custom-role]を作成し、次のロールのアクセス許可を定義します。
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- または、リソース グループに対する[ストレージ アカウント共同作成者][rbac-storage-contributor]の組み込みロールを割り当てます。

### <a name="azure-container-instances"></a>Azure Container Instances

Virtual Kubelet を使用して AKS と統合し、AKS クラスターとは別のリソース グループで Azure Container Instances (ACI) を実行する場合は、AKS サービス プリンシパルに ACI リソース グループに対する "*共同作成者*" 権限を与える必要があります。

## <a name="additional-considerations"></a>その他の注意点

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

AKS と Azure AD サービス プリンシパルを使用する場合は、以下の考慮事項を念頭に置いてください。

- Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。
- 既定では、このサービス プリンシパル資格情報は 1 年間有効です。 [サービス プリンシパルの資格情報はいつでも更新または回転][update-credentials]できます。
- すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば *https://www.contoso.org/example* ) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。
- サービス プリンシパルの **クライアント ID** を指定するときには、`appId` の値を使用します。
- Kubernetes クラスター内のエージェント ノード VM では、サービス プリンシパルの資格情報が `/etc/kubernetes/azure.json` ファイルに格納されます
- [az aks create][az-aks-create] コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の `~/.azure/aksServicePrincipal.json` ファイルに書き込まれます。
- 追加の AKS CLI コマンドでサービス プリンシパルを明示的に渡さない場合は、`~/.azure/aksServicePrincipal.json` にある既定のサービス プリンシパルが使用されます。
- 必要に応じて、aksServicePrincipal.json ファイルを削除することもできます。これにより、AKS は新しいサービス プリンシパルを作成します。
- [az aks create][az-aks-create] によって作成された AKS クラスターを削除しても、自動的に作成されたサービス プリンシパルは削除されません。
    - サービス プリンシパルを削除するには、まずクラスターの *servicePrincipalProfile.clientId* を照会し、[az ad sp delete][az-ad-sp-delete] で削除します。 次のリソース グループとクラスターの名前は、実際の値に置き換えてください。

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS と Azure AD サービス プリンシパルを使用する場合は、以下の考慮事項を念頭に置いてください。

- Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。
- 既定では、このサービス プリンシパル資格情報は 1 年間有効です。 [サービス プリンシパルの資格情報はいつでも更新または回転][update-credentials]できます。
- すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば *https://www.contoso.org/example* ) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。
- サービス プリンシパルの **クライアント ID** を指定するときには、`ApplicationId` の値を使用します。
- Kubernetes クラスター内のエージェント ノード VM では、サービス プリンシパルの資格情報が `/etc/kubernetes/azure.json` ファイルに格納されます
- [New-AzAksCluster][new-azakscluster] コマンドを使用してサービス プリンシパルを自動的に生成すると、そのサービス プリンシパルの資格情報は、このコマンドを実行するために使用されたコンピューター上のファイル `~/.azure/acsServicePrincipal.json` に書き込まれます。
- 追加の AKS PowerShell コマンドでサービス プリンシパルを明示的に渡さない場合は、`~/.azure/acsServicePrincipal.json` にある既定のサービス プリンシパルが使用されます。
- 必要に応じて、acsServicePrincipal.json ファイルを削除することもできます。これにより、AKS は新しいサービス プリンシパルを作成します。
- [New-AzAksCluster][new-azakscluster] によって作成された AKS クラスターを削除しても、自動的に作成されたサービス プリンシパルは削除されません。
    - このサービス プリンシパルを削除するには、クラスター *ServicePrincipalProfile.ClientId* に対してクエリを実行してから、[Remove-AzADServicePrincipal][remove-azadserviceprincipal] で削除します。 次のリソース グループとクラスターの名前は、実際の値に置き換えてください。

        ```azurepowershell-interactive
        $ClientId = (Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster ).ServicePrincipalProfile.ClientId
        Remove-AzADServicePrincipal -ApplicationId $ClientId
        ```
---

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

AKS クラスターのサービス プリンシパルの資格情報は、Azure CLI によってキャッシュされます。 これらの資格情報が期限切れになると、AKS クラスターのデプロイ中にエラーが発生します。 [az aks create][az-aks-create] を実行しているときの次のエラー メッセージは、キャッシュされているサービス プリンシパルの資格情報に問題があることを示す可能性があります。

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

次のコマンドを使用して、資格情報ファイルの有効期限を確認します。

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

サービス プリンシパルの資格情報の既定の有効期限は 1 年です。 *aksServicePrincipal.json* ファイルが 1 年よりも古い場合は、ファイルを削除して AKS クラスターを再度デプロイします。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS クラスターのサービス プリンシパルの資格情報は、Azure PowerShell によってキャッシュされます。 これらの資格情報が期限切れになると、AKS クラスターのデプロイ中にエラーが発生します。 [New-AzAksCluster][new-azakscluster] を実行しているときの次のエラー メッセージは、キャッシュされているサービス プリンシパルの資格情報に関する問題を示している可能性があります。

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

次のコマンドを使用して、資格情報ファイルの有効期限を確認します。

```azurepowershell-interactive
Get-ChildItem -Path $HOME/.azure/aksServicePrincipal.json
```

サービス プリンシパルの資格情報の既定の有効期限は 1 年です。 *aksServicePrincipal.json* ファイルが 1 年よりも古い場合は、ファイルを削除して AKS クラスターを再度デプロイします。

---

## <a name="next-steps"></a>次のステップ

Azure Active Directory サービス プリンシパルの詳細については、「[アプリケーション オブジェクトとサービス プリンシパル オブジェクト][service-principal]」を参照してください。

資格情報の更新方法について詳しくは、「[AKS でのサービス プリンシパルの資格情報の更新または回転][update-credentials]」を参照してください。

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
[install-the-azure-az-powershell-module]: /powershell/azure/install-az-ps
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[new-azadserviceprincipal]: /powershell/module/az.resources/new-azadserviceprincipal
[create-an-azure-service-principal-with-azure-powershell]: /powershell/azure/create-azure-service-principal-azureps
[new-azroleassignment]: /powershell/module/az.resources/new-azroleassignment
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azadserviceprincipal]: /powershell/module/az.resources/remove-azadserviceprincipal
