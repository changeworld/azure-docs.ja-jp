---
title: CLI を使用したデータ コントローラーの作成
description: CLI を使用して既に作成されている一般的なマルチノード Kubernetes クラスターに Azure Arc データ コントローラーを作成します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 836ca4cca73b71f98415c05fd89227f53332a265
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555383"
---
# <a name="create-azure-arc-data-controller-using-the-cli"></a>CLI を使用した Azure Arc データ コントローラーの作成


## <a name="prerequisites"></a>前提条件

概要情報については、「[Azure Arc データ コントローラーを作成する](create-data-controller.md)」のトピックを確認してください。

### <a name="install-tools"></a>ツールをインストールする

CLI を使用してデータ コントローラーを作成するには、Azure (az) CLI の `arcdata` 拡張機能をインストールする必要があります。 

[[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] をインストールする](install-client-tools.md)

選択するターゲット プラットフォームに関係なく、データ コントローラーの作成の前に、次の環境変数を設定する必要があります。 これらの環境変数は、データ コントローラーの作成後にメトリックとログ ダッシュボードにアクセスするために使用される資格情報になります。


### <a name="set-environment-variables"></a>環境変数の設定

メトリックとログ ダッシュボードにアクセスするために必要な 2 つの環境変数のセットを次に示します。

#### <a name="windows-powershell"></a>Windows PowerShell

```powershell
$ENV:AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
$ENV:AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
$ENV:AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
$ENV:AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```

#### <a name="linux-or-macos"></a>Linux または macOS

```console
export AZDATA_LOGSUI_USERNAME="<username for Kibana dashboard>"
export AZDATA_LOGSUI_PASSWORD="<password for Kibana dashboard>"
export AZDATA_METRICSUI_USERNAME="<username for Grafana dashboard>"
export AZDATA_METRICSUI_PASSWORD="<password for Grafana dashboard>"
```


Azure Arc データ コントローラーの作成を開始する前に、Kubernetes クラスターに接続して認証し、既存の Kubernetes コンテキストを選択しておく必要があります。 Kubernetes クラスターまたはサービスへの接続方法はさまざまです。 Kubernetes API サーバーへの接続方法については、使用している Kubernetes ディストリビューションまたはサービスのドキュメントを参照してください。

現在の Kubernetes 接続があること、および現在のコンテキストは、次のコマンドを使用して確認できます。

```console
kubectl cluster-info
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーを作成する

> [!NOTE]
> 以下の例では `az arcdata dc create` コマンドの `--namespace` パラメーターに別の値を使用できますが、必ず、以下で示す他のすべてのコマンドで `--namespace` パラメーターにその名前空間名を使用してください。

- [CLI を使用した Azure Arc データ コントローラーの作成](#create-azure-arc-data-controller-using-the-cli)
  - [前提条件](#prerequisites)
    - [Linux または macOS](#linux-or-macos)
    - [Windows PowerShell](#windows-powershell)
  - [Azure Arc データ コントローラーを作成する](#create-the-azure-arc-data-controller)
    - [Azure Kubernetes Service (AKS) に作成する](#create-on-azure-kubernetes-service-aks)
    - [Azure Stack HCI の AKS に作成する](#create-on-aks-on-azure-stack-hci)
    - [Azure Red Hat OpenShift (ARO) に作成する](#create-on-azure-red-hat-openshift-aro)
      - [カスタム デプロイ プロファイルを作成する](#create-custom-deployment-profile)
      - [データ コントローラーの作成](#create-data-controller)
    - [Red Hat OpenShift Container Platform (OCP) に作成する](#create-on-red-hat-openshift-container-platform-ocp)
      - [ストレージ クラスを定義する](#determine-storage-class)
      - [カスタム デプロイ プロファイルを作成する](#create-custom-deployment-profile-1)
      - [ストレージ クラスを設定する](#set-storage-class)
      - [LoadBalancer を設定する (省略可能)](#set-loadbalancer-optional)
      - [データ コントローラーの作成](#create-data-controller-1)
    - [オープンソースのアップストリーム Kubernetes (kubeadm) に作成する](#create-on-open-source-upstream-kubernetes-kubeadm)
    - [AWS Elastic Kubernetes Service (EKS) に作成する](#create-on-aws-elastic-kubernetes-service-eks)
    - [Google Cloud Kubernetes Engine Service (GKE) に作成する](#create-on-google-cloud-kubernetes-engine-service-gke)
  - [作成状態の監視](#monitoring-the-creation-status)
  - [作成の問題のトラブルシューティング](#troubleshooting-creation-problems)

### <a name="create-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) に作成する

既定では、AKS デプロイ プロファイルでは `managed-premium` ストレージ クラスが使用されます。 `managed-premium` ストレージ クラスは、Premium ディスクを含む VM イメージを使用してデプロイされた VM がある場合にのみ機能します。

ストレージ クラスとして `managed-premium` を使用する場合は、次のコマンドを実行してデータ コントローラーをデプロイできます。 コマンドのプレースホルダーを、リソース グループ名、サブスクリプション ID、Azure の場所に置き換えます。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace <namespace> --name arc --azure-subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --use-k8s

#Example:
#az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace arc --name arc --azure-subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --use-k8s
```

どのストレージ クラスを使用するかわからない場合は、使用している VM の種類に関係なくサポートされる `default` ストレージ クラスを使用してください。 最速のパフォーマンスが得られなくなるだけです。

`default` ストレージ クラスを使用する場合は、次のコマンドを実行できます。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-default-storage --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-default-storage  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-aks-on-azure-stack-hci"></a>Azure Stack HCI の AKS に作成する

#### <a name="configure-storage-azure-stack-hci-with-aks-hci"></a>ストレージを構成する (Azure Stack HCI と AKS-HCI)

AKS-HCI で Azure Stack HCI を使用している場合は、`fsType` を使用してカスタム ストレージ クラスを作成します。

   ```json
   fsType: ext4
   ```

この型を使用して、データ コントローラーをデプロイします。 詳細な手順については、[AKS on Azure Stack HCI ディスクのカスタム ストレージ クラスを作成する](/azure-stack/aks-hci/container-storage-interface-disks#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk)を参照してください。

既定では、デプロイ プロファイルでは `default` という名前のストレージ クラスとサービス タイプ `LoadBalancer` が使用されます。

次のコマンドを実行すると、`default` ストレージ クラスとサービス タイプ `LoadBalancer` を使用してデータ コントローラーを作成できます。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。


### <a name="create-on-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) に作成する

#### <a name="create-custom-deployment-profile"></a>カスタム デプロイ プロファイルを作成する

Azure RedHat Open Shift には、プロファイル `azure-arc-azure-openshift` を使用します。

```azurecli
az arcdata dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>データ コントローラーの作成

次のコマンドを実行して、データ コントローラーを作成します。

```azurecli
az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Red Hat OpenShift Container Platform (OCP) に作成する

#### <a name="determine-storage-class"></a>ストレージ クラスを定義する

次のコマンドを実行して、使用するストレージ クラスを決定する必要もあります。

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>カスタム デプロイ プロファイルを作成する

次のコマンドを実行して、`azure-arc-openshift` デプロイ プロファイルに基づいて新しいカスタムのデプロイ プロファイル ファイルを作成します。 このコマンドでは、`custom` というディレクトリが現在の作業ディレクトリに作成され、カスタムのデプロイ プロファイル ファイル `control.json` がそのディレクトリに作成されます。

OpenShift Container Platform には、プロファイル `azure-arc-openshift` を使用します。

```azurecli
az arcdata dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>ストレージ クラスを設定する 

次に、下記のコマンドの `<storageclassname>` を、(上記の `kubectl get storageclass` コマンドを実行して決定された) 使用するストレージ クラスの名前に置き換えることによって、目的のストレージ クラスを設定します。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>LoadBalancer を設定する (省略可能)

既定では、`azure-arc-openshift` デプロイ プロファイルでは `NodePort` がサービス タイプとして使用されます。 ロード バランサーと統合された OpenShift クラスターを使用している場合は、次のコマンドを使用して、`LoadBalancer` サービス タイプを使用するように構成を変更できます。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="create-data-controller"></a>データ コントローラーの作成

これで、次のコマンドを使用してデータ コントローラーを作成する準備ができました。

> [!NOTE]
>   `--path` パラメーターは、control.json ファイル自体ではなく、control.json ファイルを含む _ディレクトリ_ を指している必要があります。

> [!NOTE]
>   OpenShift Container Platform にデプロイする場合は、`--infrastructure` パラメーター値を指定する必要があります。  オプションは、`aws`、`azure`、`alibaba`、`gcp`、`onpremises` です。

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>オープンソースのアップストリーム Kubernetes (kubeadm) に作成する

既定では、kubeadm デプロイ プロファイルでは、`local-storage` という名前のストレージ クラスとサービス タイプ `NodePort` が使用されます。 これで問題ない場合は、目的のストレージ クラスとサービス タイプを設定する次の手順をスキップして、後述する `az arcdata dc create` コマンドをすぐに実行できます。

デプロイ プロファイルをカスタマイズして特定のストレージ クラスやサービス タイプを指定する場合は、まず次のコマンドを実行して、kubeadm デプロイ プロファイルに基づいて新しいカスタムのデプロイ プロファイル ファイルを作成します。 このコマンドでは、`custom` というディレクトリが現在の作業ディレクトリに作成され、カスタムのデプロイ プロファイル ファイル `control.json` がそのディレクトリに作成されます。

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path ./custom 
```

次のコマンドを実行して、使用可能なストレージ クラスを検索できます。

```console
kubectl get storageclass
```

次に、下記のコマンドの `<storageclassname>` を、(上記の `kubectl get storageclass` コマンドを実行して決定された) 使用するストレージ クラスの名前に置き換えることによって、目的のストレージ クラスを設定します。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

既定では、kubeadm デプロイ プロファイルでは `NodePort` がサービス タイプとして使用されます。 ロードバランサーと統合された Kubernetes クラスターを使用している場合は、次のコマンドを使用して構成を変更できます。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer" --k8s-namespace <namespace> --use-k8s
```

これで、次のコマンドを使用してデータ コントローラーを作成する準備ができました。

> [!NOTE]
>   OpenShift Container Platform にデプロイする場合は、`--infrastructure` パラメーター値を指定する必要があります。  オプションは、`aws`、`azure`、`alibaba`、`gcp`、`onpremises` です。

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom - --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>AWS Elastic Kubernetes Service (EKS) に作成する

既定では、EKS のストレージ クラスは `gp2` で、サービス タイプは `LoadBalancer` です。

次のコマンドを実行し、提供されている EKS デプロイ プロファイルを使用してデータ コントローラーを作成します。

```azurecli
az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Google Cloud Kubernetes Engine Service (GKE) に作成する

既定では、GKE のストレージ クラスは `standard` で、サービス タイプは `LoadBalancer` です。

次のコマンドを実行し、提供されている GKE デプロイ プロファイルを使用してデータ コントローラーを作成します。

```azurecli
az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、`arc` という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。 異なる名前の名前空間/データ コントローラーを使用している場合は、`arc` をその名前で置き換えてください。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。 これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。
