---
title: Azure Arc データ コントローラーを作成する | 直接接続モード
description: データ コントローラーを直接接続モードで作成する方法について説明します。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: fb1952c00b54fd4618d3c4d3a830d862eb50a439
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301762"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>CLI を使用して直接接続モードで Azure Arc データ コントローラーを作成する

この記事では、この機能の現在のプレビューで、CLI を使用して Azure Arc データ コントローラーを **直接** 接続モードで作成する方法について説明します。 


## <a name="complete-prerequisites"></a>前提条件を満たす

開始する前に、[直接接続モードでデータ コントローラーのデプロイするための前提条件](create-data-controller-direct-prerequisites.md)に関するページに記載されている前提条件を満たしていることを確認してください。

Azure Arc データ コントローラーを **直接** 接続モードで作成するには、次の手順を行う必要があります。

1. Azure Arc 対応データ サービス拡張機能を作成する。 
1. カスタムの場所を作成する。
1. データ コントローラーを作成します。

> [!NOTE]
> 現時点では、この手順はポータルからのみ実行できます。 詳細については、[リリース ノート](release-notes.md)をご覧ください。 

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Azure Arc 対応データ サービス拡張機能を作成する

データ サービス拡張機能を作成するには、k8s-extension CLI を使用します。

### <a name="set-environment-variables"></a>環境変数の設定

以下の環境変数を設定してください。次の手順で使用します。

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
export AZDATA_LOGSUI_USERNAME=<username for Kibana dashboard>
export AZDATA_LOGSUI_PASSWORD=<password for Kibana dashboard>
export AZDATA_METRICSUI_USERNAME=<username for Grafana dashboard>
export AZDATA_METRICSUI_PASSWORD=<password for Grafana dashboard>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

### <a name="create-the-arc-data-services-extension"></a>Arc データ サービス拡張機能を作成する

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>プライベート コンテナー レジストリと資格情報を使用して Azure Arc データ サービス拡張機能をデプロイする

プライベート リポジトリからデプロイする場合は、次のコマンドを使用します。

```azurecli
az k8s-extension create -c "<connected cluster name>" -g "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 次に例を示します。
```azurecli
az k8s-extension create -c "my-connected-cluster" -g "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> Arc データ サービス拡張機能のインストールは、完了するまでに数分かかる場合があります。

### <a name="verify-the-arc-data-services-extension-is-created"></a>Arc データ サービス拡張機能が作成されていることを確認する

Azure Arc 対応データ サービス拡張機能が作成されているかどうかは、ポータルから確認することも、Azure Arc 対応 Kubernetes クラスターに直接接続して確認することもできます。 

#### <a name="azure-portal"></a>Azure portal
1. Azure portal にログインし、Kubernetes に接続されたクラスター リソースがあるリソース グループに移動します。
1. 拡張機能がデプロイされた Azure Arc 対応 Kubernetes クラスター (タイプ = "Kubernetes - Azure Arc") を選択します。
1. 左側のナビゲーションの **[設定]** で **[拡張機能]** を選択します。
1. 前に作成した拡張機能がインストール済みの状態で表示されます。

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="拡張機能ダッシュボード":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. ターミナル ウィンドウから Kubernetes クラスターに接続します。
1. 次のコマンドを実行し、(1) 前述の名前空間が作成されていることと、(2) `bootstrapper` ポッドの状態が "実行中" になっていることを確認してから、次の手順に進みます。

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

たとえば、次の例を実行すると、`arc` 名前空間からポッドが取得されます。

```console
#Example:
kubectl get pods -n arc
```

## <a name="retrieve-the-managed-identity-and-grant-roles"></a>マネージド ID を取得してロールを付与する

Arc データ サービス拡張機能の作成時に作成されるマネージ ID には、使用状況やメトリックが自動的にアップロードされるように特定のロールを割り当てる必要があります。

### <a name="1-retrieve-managed-identity-of-the-arc-data-controller-extension"></a>(1) Arc データ コントローラー拡張機能のマネージド ID を取得する

```powershell
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group <resource group>  --cluster-name <connectedclustername> --cluster-type connectedClusters --name <name of extension> | convertFrom-json).identity.principalId
#Example
$Env:MSI_OBJECT_ID = (az k8s-extension show --resource-group myresourcegroup  --cluster-name myconnectedcluster --cluster-type connectedClusters --name ads-extension | convertFrom-json).identity.principalId
```

### <a name="2-assign-role-to-the-managed-identity"></a>(2) マネージド ID にロールを割り当てる

次のコマンドを実行して、**共同作成者** と **監視メトリック発行者** のロールを割り当てます。
```powershell
az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Contributor" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

az role assignment create --assignee $Env:MSI_OBJECT_ID --role "Monitoring Metrics Publisher" --scope "/subscriptions/$ENV:subscription/resourceGroups/$ENV:resourceGroup"

```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>カスタムの場所の CLI 拡張機能を使用してカスタムの場所を作成する

カスタムの場所とは、Kubernetes クラスターの名前空間に相当する Azure リソースです。  カスタムの場所は、Azure との間でリソースをデプロイする際のターゲットとして使用されます。 カスタムの場所の詳細については、「[Azure Arc 対応 Kubernetes でのカスタムの場所](../kubernetes/conceptual-custom-locations.md)」のドキュメントを参照してください。

### <a name="set-environment-variables"></a>環境変数の設定

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"

$ENV:hostClusterId=(az connectedk8s show -g $ENV:resourceGroup -n $ENV:resourceName --query id -o tsv)
$ENV:extensionId=(az k8s-extension show -g $ENV:resourceGroup -c $ENV:resourceName --cluster-type connectedClusters --name $ENV:ADSExtensionName --query id -o tsv)

az customlocation create -g $ENV:resourceGroup -n $ENV:clName --namespace $ENV:clNamespace --host-resource-id $ENV:hostClusterId --cluster-extension-ids $ENV:extensionId
```

## <a name="validate--the-custom-location-is-created"></a>カスタムの場所が作成されたことを確認する

ターミナルから次のコマンドを実行して、カスタムの場所を一覧表示し、 **[Provisioning State]\(プロビジョニングの状態\)** に "Succeeded (成功)" と表示されていることを確認します。

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーを作成する

拡張機能とカスタムの場所が作成されたら、引き続き次のように Azure Arc データ コントローラーをデプロイします。

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --profile-name <profile name>  --auto-upload-logs true --custom-location <name of custom location>
# Example
az arcdata dc create -n arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --profile-name azure-arc-aks-premium-storage  --auto-upload-logs true --custom-location mycustomlocation
```

カスタム構成テンプレートを使用して Azure Arc データ コントローラーを作成する場合は、[カスタム構成プロファイルの作成](create-custom-configuration-template.md)に関する記事で説明されている手順に従い、ファイルへのパスを次のように指定します。

```
az arcdata dc create --name <name> --resource-group <resourcegroup> --location <location> --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --custom-location <name of custom location>
# Example
az arcdata dc create --name arc-dc1 --resource-group my-resource-group --location eastasia --connectivity-mode direct --path ./azure-arc-custom  --auto-upload-logs true --custom-location mycustomlocation
```


## <a name="monitor-the-creation"></a>作成を監視する

Azure portal のデプロイ状態にデプロイが成功したことが示されたら、次のように、クラスターへの Arc データ コントローラーのデプロイの状態を確認できます。

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>次の手順

[Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md)

[Azure Arc で Azure SQL Managed Instance を作成する](create-sql-managed-instance.md)
