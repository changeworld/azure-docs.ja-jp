---
title: Azure Arc データ コントローラーをデプロイする | 直接接続モード
description: データ コントローラーを直接接続モードでデプロイする方法について説明します。
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031350"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Azure Arc データ コントローラーをデプロイする | 直接接続モード

この記事では、Azure Arc データ コントローラーを直接接続モードでデプロイする方法について説明します (この機能は現在プレビュー段階です)。 

現在は、Azure Arc データ コントローラーを Azure portal から作成できます。 Azure Arc 対応データ サービス向けのその他のツールでは、直接接続モードでのデータ コントローラーの作成がサポートされていません。 詳細については、「[既知の問題 - Azure Arc 対応データ サービス (プレビュー)](known-issues.md)」を参照してください。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>前提条件を満たす

開始する前に、[直接接続モードでデータ コントローラーのデプロイするための前提条件](deploy-data-controller-direct-mode-prerequisites.md)に関するページに記載されている前提条件を満たしていることを確認してください。

この記事では、次のタスクを実行する方法について大まかに説明します。

1. Azure Arc 対応データ サービス拡張機能を作成する。 
1. カスタムの場所を作成する。
1. ポータルからデータ コントローラーをデプロイする。

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
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Arc データ サービス拡張機能を作成する

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Arc データ サービス拡張機能のインストールは、完了するまでに数分かかる場合があります。

### <a name="verify-the-arc-data-services-extension-is-created"></a>Arc データ サービス拡張機能が作成されていることを確認する

Arc 対応データ サービス拡張機能が作成されているかどうかは、ポータルから確認することも、Arc 対応 Kubernetes クラスターに直接接続して確認することもできます。 

#### <a name="azure-portal"></a>Azure portal
1. Azure portal にログインし、Kubernetes に接続されたクラスター リソースがあるリソース グループに移動します。
1. 拡張機能がデプロイされた Arc 対応 Kubernetes クラスター (タイプ = "Kubernetes - Azure Arc") を選択します。
1. 左側のナビゲーションの **[設定]** で [拡張機能 (プレビュー)] を選択します。
1. 先ほど作成した拡張機能が "インストール済み" の状態で表示されます。

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
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>カスタムの場所が作成されたことを確認する

ターミナルから次のコマンドを実行して、カスタムの場所を一覧表示し、 **[Provisioning State]\(プロビジョニングの状態\)** に "Succeeded (成功)" と表示されていることを確認します。

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーを作成する

拡張機能とカスタムの場所が作成されたら、Azure portal に進み、Azure Arc データ コントローラーをデプロイします。

1. Azure Portal にログインします。
1. Azure Marketplace で "Azure Arc data controller" を検索し、フローの作成を開始します。
1. **[前提条件]** セクションで [Azure Arc enabled Kubernetes cluster (direct mode)]\(Azure Arc 対応 Kubernetes クラスター (直接モード)\) が選択されていることを確認し、次の手順に進みます。
1. **[Data controller details]\(データ コントローラーの詳細\)** セクションで、サブスクリプションとリソース グループを選択します。
1. データ コントローラーの名前を入力します。
1. デプロイ先の Kubernetes ディストリビューション プロバイダーに応じて構成プロファイルを選択します。
1. 前の手順で作成したカスタムの場所を選択します。
1. データ コントローラー管理者のログインとパスワードの詳細を入力します。
1. Azure オブジェクトの作成に使用されるサービス プリンシパルの ClientId、TenantId、クライアント シークレットの詳細を入力します。 サービス プリンシパル アカウントとそのアカウントに付与する必要のあるロールの作成に関する詳しい手順については、[メトリックのアップロード](upload-metrics-and-logs-to-azure-monitor.md)に関するページを参照してください。
1. **[次へ]** をクリックし、概要ページですべての詳細を確認し、 **[作成]** をクリックします。

## <a name="monitor-the-creation"></a>作成を監視する

Azure portal のデプロイ状態にデプロイが成功したことが示されたら、次のように、クラスターへの Arc データ コントローラーのデプロイの状態を確認できます。

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>次の手順

[Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md)

[Azure Arc で Azure SQL Managed Instance を作成する](create-sql-managed-instance.md)
