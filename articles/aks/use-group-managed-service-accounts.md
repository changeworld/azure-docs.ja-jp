---
title: Azure Kubernetes Service (AKS) クラスター上の Windows Server ノードでグループ管理サービス アカウント (GMSA) を有効にする (プレビュー)
description: ポッドをセキュリティで保護するために、Azure Kubernetes Service (AKS) クラスター上の Windows Server ノードでグループ管理サービス アカウント (GMSA) を有効にする方法について説明します。
services: container-service
ms.topic: article
ms.date: 11/01/2021
ms.openlocfilehash: f0acd8cc19ab8a0994b6218a171d8343eef21096
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477860"
---
# <a name="enable-group-managed-service-accounts-gmsa-for-you-windows-server-nodes-on-your-azure-kubernetes-service-aks-cluster-preview"></a>Azure Kubernetes Service (AKS) クラスター上の Windows Server ノードでグループ管理サービス アカウント (GMSA) を有効にする (プレビュー)

[グループ管理サービス アカウント (GMSA)][gmsa-overview] は、パスワードの自動管理、簡略化されたサービス プリンシパル名 (SPN) の管理、および管理を他の管理者に委任する機能を提供する複数のサーバーのマネージド ドメイン アカウントです。 AKS では、Windows Server ノードで GMSA を有効にする機能が提供されます。これにより、Windows Server ノード上で実行されているコンテナーを GMSA と統合して GMSA で管理できます。

AKS 上の Windows Server ノードでの GMSA の有効化は、プレビュー段階にあります。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="pre-requisites"></a>前提条件

AKS 上の Windows Server ノードで GMSA を有効にするには、次のものが必要となります。

* Kubernetes 1.19 以上。
* `aks-preview` 拡張機能バージョン 0.5.37 以上。
* Docker コンテナー ランタイム (現在は既定です)。
* AKS クラスターの[マネージド ID][aks-managed-id]。
* Azure Key Vault を作成または更新するためのアクセス許可。
* Active Directory Domain Services またはオンプレミスの Active Directory で GMSA を構成するためのアクセス許可。
* ドメイン コントローラーで Active Directory Web サービスが有効になっている必要があります。また、AKS クラスターからポート 9389 に到達できる必要があります。

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI をインストールする

*aks-preview* Azure CLI 拡張機能が必要になります。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-akswindowsgmsapreview-preview-feature"></a>`AKSWindowsGmsaPreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `AKSWindowsGmsaPreview` 機能フラグも有効にする必要があります。

`AKSWindowsGmsaPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKSWindowsGmsaPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSWindowsGmsaPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configure-gmsa-on-active-directory-domain-controller"></a>Active Directory ドメイン コントローラーに GMSA を構成する

AKS で GMSA を使用するには、ドメイン コントローラーに構成されている GMSA 資格情報にアクセスするために、GMSA と標準のドメイン ユーザーの資格情報の両方が必要です。 ドメイン コントローラーに GMSA を構成するには、[グループ管理サービス アカウントの概要][gmsa-getting-started]に関する記事を参照してください。 標準のドメイン ユーザーの資格情報の場合は、GMSA の資格情報にアクセスできる限り、既存のユーザーを使用するか、新しいユーザーを作成できます。

> [!IMPORTANT]
> Active Directory Domain Services またはオンプレミスの Active Directory を使用する必要があります。 現時点では、Azure Active Directory を使用して AKS クラスターで GMSA を構成することはできません。

## <a name="store-the-standard-domain-user-credentials-in-azure-key-vault"></a>標準のドメイン ユーザーの資格情報を Azure Key Vault に格納する

AKS クラスターでは、標準のドメイン ユーザーの資格情報を使用して、ドメイン コントローラーから GMSA の資格情報にアクセスします。 AKS クラスターにそれらの資格情報への安全なアクセスを提供するには、それらの資格情報を Azure Key Vault に格納する必要があります。 新しいキー コンテナーを作成するか、既存のキー コンテナーを使用できます。

標準のドメイン ユーザーの資格情報をシークレットとしてキー コンテナーに格納するには、`az keyvault secret set` を使用します。 次の例では、ドメイン ユーザーの資格情報を *MyAKSGMSAVault* キー コンテナーにキー *GMSADomainUserCred* で格納します。 パラメーターを独自のキー コンテナー、キー、ドメイン ユーザーの資格情報に置き換える必要があります。

```azurecli
az keyvault secret set --vault-name MyAKSGMSAVault --name "GMSADomainUserCred" --value "$Domain\\$DomainUsername:$DomainUserPassword"
```

## <a name="optional-use-a-custom-vnet-with-custom-dns"></a>オプション: カスタム DNS を持つカスタム VNET を使用する

ドメイン コントローラーは、AKS クラスターから到達できるように、DNS を使用して構成する必要があります。 AKS クラスターの外部にネットワークと DNS を構成して、クラスターからドメイン コントローラーにアクセスできるようすることができます。 または、AKS クラスターで Azure CNI を使用してカスタム DNS を持つカスタム VNET を構成し、ドメイン コントローラーへのアクセスを提供することもできます。 詳細については、「[Azure Kubernetes Service (AKS) で Azure CNI ネットワークを構成する][aks-cni]」を参照してください。

## <a name="optional-use-your-own-kubelet-identity-for-your-cluster"></a>オプション: クラスターに独自の kubelet ID を使用する

AKS クラスターからキー コンテナーにアクセスできるようにするには、クラスターの kubelet ID でキー コンテナーにアクセスする必要があります。 既定では、マネージド ID が有効になっているクラスターを作成すると、kubelet ID が自動的に作成されます。 後の手順でクラスターを作成した後に、キー コンテナーへのアクセス権をこの ID に付与できます。

または、独自の ID を作成し、後の手順でクラスターを作成するときにこの ID を使用することもできます。 提供されるマネージド ID の詳細については、「[マネージド ID の概要][aks-managed-id-kubelet]」を参照してください。

独自の ID を作成するには、`az identity create` を使用して ID を作成します。 次の例では、リソース グループ *myResourceGroup* に *myIdentity* という ID を作成します。

```azurecli
az identity create --name myIdentity --resource-group myResourceGroup
```

クラスターの作成前または作成後に、キー コンテナーへのアクセス権を kubelet ID に付与できます。 次の例では、`az identity list` を使用して ID を取得し、それを *MANAGED_ID* に設定してから、`az keyvault set-policy` を使用して *MyAKSGMSAVault* キー コンテナーへのアクセス権をその ID に付与します。

```azurecli
MANAGED_ID=$(az identity list --query "[].id" -o tsv)
az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="create-aks-cluster"></a>AKS クラスターの作成

AKS クラスターで GMSA を使用するには、*enable-windows-gmsa*、*gmsa-dns-server*、*gmsa-root-domain-name*、*enable-managed-identity* パラメーターを使用します。

> [!NOTE]
> Windows Server ノード プールを使用してクラスターを作成する場合は、クラスターの作成時に管理者の資格情報を指定する必要があります。 次のコマンドを実行すると、ユーザー名の入力が求められ、後のコマンドで使用できるように WINDOWS_USERNAME と設定されます (この記事に示すコマンドは BASH シェルで入力されます)。
> 
> ```azurecli
> echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
> ```

`az aks create` を使用して AKS クラスターを作成してから、`az aks nodepool add` を使用して Windows Server ノード プールを追加します。 次の例では、*MyResourceGroup* リソース グループに *MyAKS* クラスターを作成し、GMSA を有効にして、*npwin* という新しいノード プールを追加します。

> [!NOTE]
> カスタム VNet を使用している場合は、*vnet-subnet-id* を使用して VNet の ID を指定する必要があります。また、構成に応じて、*docker-bridge-address*、*dns-service-ip*、*service-cidr* も追加する必要がある場合があります。
> 
> kubelet ID 用に独自の ID を作成した場合は、*assign-kubelet-identity* パラメーターを使用して ID を指定します。

```azurecli
DNS_SERVER=<IP address of DNS server>
ROOT_DOMAIN_NAME="contoso.com"

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure \
    --load-balancer-sku standard \
    --windows-admin-username $WINDOWS_USERNAME \
    --enable-managed-identity \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME

az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKS \
    --os-type Windows \
    --name npwin \
    --node-count 1    
```

`az aks update` を使用して、Windows Server ノードとマネージド ID が既にある既存のクラスターで GMSA を有効にすることもできます。 次に例を示します。

```azurecli
az aks update \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME
```

クラスターを作成または更新した後に、`az keyvault set-policy` を使用して、キー コンテナーへのアクセス権を ID に付与します。 次の例では、クラスターによって作成された kubelet ID に *MyAKSGMSAVault* キー コンテナーへのアクセスを許可します。

> [!NOTE]
> kubelet ID に独自の ID を指定した場合は、このステップをスキップします。

```azurecli
MANAGED_ID=$(az aks show -g MyResourceGroup -n MyAKS --query "identityProfile.kubeletidentity.objectId" -o tsv)

az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="install-gmsa-cred-spec"></a>GMSA の資格情報の仕様をインストールする

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][] コマンドを使用します。 次の例では、*MyResourceGroup* の *MyAKS* という名前の AKS クラスター用の資格情報が取得されます。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

以下の内容で *gmsa-spec.yaml* を作成し、プレースホルダーを独自の値に置き換えます。

```yml
apiVersion: windows.k8s.io/v1alpha1
kind: GMSACredentialSpec
metadata:
  name: aks-gmsa-spec  # This name can be changed, but it will be used as a reference in the pod spec
credspec:
  ActiveDirectoryConfig:
    GroupManagedServiceAccounts:
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $NETBIOS_DOMAIN_NAME
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $DNS_DOMAIN_NAME
    HostAccountConfig:
      PluginGUID: '{CCC2A336-D7F3-4818-A213-272B7924213E}'
      PortableCcgVersion: "1"
      PluginInput: ObjectId=$MANAGED_ID;SecretUri=$SECRET_URI  # SECRET_URI takes the form https://$akvName.vault.azure.net/secrets/$akvSecretName
  CmsPlugins:
  - ActiveDirectory
  DomainJoinConfig:
    DnsName: $DNS_DOMAIN_NAME
    DnsTreeName: $DNS_ROOT_DOMAIN_NAME
    Guid:  $AD_DOMAIN_OBJECT_GUID
    MachineAccountName: $GMSA_ACCOUNT_USERNAME
    NetBiosName: $NETBIOS_DOMAIN_NAME
    Sid: $GMSA_SID
```

次の内容で *gmsa-role.yaml* を作成します。

```yml
#Create the Role to read the credspec
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: aks-gmsa-role
rules:
- apiGroups: ["windows.k8s.io"]
  resources: ["gmsacredentialspecs"]
  verbs: ["use"]
  resourceNames: ["aks-gmsa-spec"]
```

次の内容で *gmsa-role-binding.yaml* を作成します。

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: allow-default-svc-account-read-on-aks-gmsa-spec
  namespace: default
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
roleRef:
  kind: ClusterRole
  name: aks-gmsa-role
  apiGroup: rbac.authorization.k8s.io
```

`kubectl apply` を使用して、*gmsa-spec.yaml*、*gmsa-role.yaml*、*gmsa-role-binding.yaml* から変更を適用します。

```azurecli
kubectl apply -f gmsa-spec.yaml
kubectl apply -f gmsa-role.yaml
kubectl apply -f gmsa-role-binding.yaml
```

## <a name="verify-gmsa-is-installed-and-working"></a>GMSA がインストールされて動作していることを確認する

次の内容で *gmsa-demo.yaml* を作成します。

```yml
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
   app: gmsa-demo
  name: gmsa-demo
  namespace: default
data:
  run.ps1: |
   $ErrorActionPreference = "Stop"

   Write-Output "Configuring IIS with authentication."

   # Add required Windows features, since they are not installed by default.
   Install-WindowsFeature "Web-Windows-Auth", "Web-Asp-Net45"

   # Create simple ASP.Net page.
   New-Item -Force -ItemType Directory -Path 'C:\inetpub\wwwroot\app'
   Set-Content -Path 'C:\inetpub\wwwroot\app\default.aspx' -Value 'Authenticated as <B><%=User.Identity.Name%></B>, Type of Authentication: <B><%=User.Identity.AuthenticationType%></B>'

   # Configure IIS with authentication.
   Import-Module IISAdministration
   Start-IISCommitDelay
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/windowsAuthentication').Attributes['enabled'].value = $true
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/anonymousAuthentication').Attributes['enabled'].value = $false
   (Get-IISServerManager).Sites[0].Applications[0].VirtualDirectories[0].PhysicalPath = 'C:\inetpub\wwwroot\app'
   Stop-IISCommitDelay

   Write-Output "IIS with authentication is ready."

   C:\ServiceMonitor.exe w3svc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gmsa-demo
  template:
    metadata:
      labels:
        app: gmsa-demo
    spec:
      securityContext:
        windowsOptions:
          gmsaCredentialSpecName: aks-gmsa-spec
      containers:
      - name: iis
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: IfNotPresent
        command:
         - powershell
        args:
          - -File
          - /gmsa-demo/run.ps1
        volumeMounts:
          - name: gmsa-demo
            mountPath: /gmsa-demo
      volumes:
      - configMap:
          defaultMode: 420
          name: gmsa-demo
        name: gmsa-demo
      nodeSelector:
        kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: gmsa-demo
  type: LoadBalancer
```

`kubectl apply` を使用して、*gmsa-demo.yaml* から変更を適用します

```azurecli
kubectl apply -f gmsa-demo.yaml
```

`kubectl get service` を使用して、サンプル アプリケーションの IP アドレスを表示します。

```console
kubectl get service gmsa-demo --watch
```

最初は、*gmsa-demo* サービスの *EXTERNAL-IP* が "*保留中*" として表示されます。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
gmsa-demo          LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* アドレスが "*保留中*" から実際のパブリック IP アドレスに変わったら、`CTRL-C` を使用して `kubectl` ウォッチ プロセスを停止します。 次の出力例は、サービスに割り当てられている有効なパブリック IP アドレスを示しています。

```output
gmsa-demo  LoadBalancer   10.0.37.27   EXTERNAL-IP   80:30572/TCP   2m
```

GMSA が動作していて正しく構成されていることを確認するには、*gmsa-demo* サービスの外部 IP アドレスを指定して Web ブラウザーを開きます。 `$NETBIOS_DOMAIN_NAME\$AD_USERNAME` とパスワードを使用して認証し、`Authenticated as $NETBIOS_DOMAIN_NAME\$AD_USERNAME, Type of Authentication: Negotiate` が表示されることを確認します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="no-authentication-is-prompted-when-loading-the-page"></a>ページを読み込むときに認証を求められない

ページを読み込んでも認証を求めるメッセージが表示されない場合は、`kubelet logs POD_NAME` を使用してポッドのログを表示し、"*認証が装備された IIS の準備ができています*" と表示されていることを確認します。

### <a name="connection-timeout-when-trying-to-load-the-page"></a>ページの読み込み時に接続がタイムアウトする

ページを読み込もうとしたときに接続タイムアウトが発生した場合は、`kubectl get pods --watch` を使用してサンプル アプリが実行されていることを確認します。 サンプル アプリ ポッドが実行される前に、サンプル アプリ サービスの外部 IP アドレスが使用できる場合があります。

### <a name="pod-fails-to-start-and-an-winapi-error-shows-in-the-pod-events"></a>ポッドの起動に失敗し、ポッド イベントに "*winapi エラー*" が表示される

`kubectl get pods --watch` を実行して数分待った後に、ポッドが起動しない場合は `kubectl describe pod POD_NAME` を実行します。 ポッド イベントに "*winapi エラー*" が表示される場合は、GMSA 資格情報の仕様の構成でエラーが発生している可能性があります。 *gmsa-spec.yaml* で置き換えたすべての値が正しいことを確認し、`kubectl apply -f gmsa-spec.yaml` を再実行して、サンプル アプリケーションを再デプロイします。


[aks-cni]: configure-azure-cni.md
[aks-managed-id]: use-managed-identity.md
[aks-managed-id-kubelet]: use-managed-identity.md#summary-of-managed-identities
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[gmsa-getting-started]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[rdp]: rdp.md