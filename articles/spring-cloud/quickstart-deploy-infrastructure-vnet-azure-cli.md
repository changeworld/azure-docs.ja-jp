---
title: クイックスタート - Azure CLI を使用して Azure Spring Cloud をプロビジョニングする
description: このクイックスタートでは、Azure CLI を使用して Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。
services: azure-cli
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-java
ms.author: vramasubbu
ms.date: 11/12/2021
ms.openlocfilehash: 967303cc675ebc6537630f08e6871f5b32cf6af4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488832"
---
# <a name="quickstart-provision-azure-spring-cloud-using-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure Spring Cloud をプロビジョニングする

このクイックスタートでは、Azure CLI を使用して Azure Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。

Azure Spring Cloud では、コードを変更せずに、Spring Boot マイクロサービス アプリケーションを Azure に簡単にデプロイできます。 Spring Cloud アプリケーションのインフラストラクチャはこのサービスによって管理されるので、開発者はコードに専念できます。 Azure Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、ブルー/グリーン デプロイなどを使用して、ライフサイクル管理を提供します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* Azure Spring Cloud クラスター用の 2 つの専用サブネット (1 つはサービス ランタイム用、もう 1 つは Spring Boot マイクロサービス アプリケーション用)。 サブネットと仮想ネットワークの要件については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」セクションを参照してください。
* Azure Spring Cloud の診断設定用の既存の Log Analytics ワークスペースと、ワークスペースベースの Application Insights リソース。 詳細については、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」と「[Azure Spring Cloud での Application Insights Java In-Process Agent](how-to-application-insights.md)」を参照してください。
* Azure Spring Cloud クラスターに使用するために指定した 3 つの内部クラスレス ドメイン間ルーティング (CIDR) の範囲 (それぞれ少なくとも */16*)。 これらの CIDR の範囲は直接ルーティング可能ではなく、Azure Spring Cloud クラスターによって内部的にのみ使用されます。 内部 Spring Cloud の CIDR の範囲に *169.254.0.0/16*、*172.30.0.0/16*、*172.31.0.0/16*、または *192.0.2.0/24* をクラスターに使用することはできません。また、クラスター仮想ネットワークのアドレス範囲に含まれる IP 範囲も使用できません。
* 仮想ネットワークに付与されたサービスのアクセス許可。 仮想ネットワーク上の専用かつ動的なサービス プリンシパルにさらに高度なデプロイやメンテナンスの権限を付与するには、Azure Spring Cloud リソース プロバイダーに仮想ネットワークの所有者としてのアクセス許可が必要です。 手順と詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークにサービス アクセス許可を付与する](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)」セクションを参照してください。
* Azure Firewall またはネットワーク仮想アプライアンス (NVA) を使用している場合は、次の前提条件も満たす必要があります。
   * ネットワークと完全修飾ドメイン名 (FQDN) の規則。 詳細については、「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」を参照してください。
   * サービス ランタイムと Spring Boot マイクロサービス アプリケーションの各サブネットに適用される一意のユーザー定義ルート (UDR)。 UDR の詳細については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。 Spring Cloud クラスターをデプロイする前に、UDR に NVA を宛先とする *0.0.0.0/0* のルートを構成しておく必要があります。 詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[独自のルート テーブルを使用する](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)」セクションを参照してください。
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="review-the-azure-cli-deployment-script"></a>Azure CLI デプロイ スクリプトを確認する

このクイックスタートで使用するデプロイ スクリプトは、「[Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)」のものです。

```azurecli
#!/bin/bash

echo "Enter Azure Subscription ID: "
read subscription
subscription=$subscription

echo "Enter Azure region for resource deployment: "
read region
location=$region

echo "Enter Azure Spring cloud Resource Group Name: "
read azurespringcloudrg
azurespringcloud_resource_group_name=$azurespringcloudrg

echo "Enter Azure Spring cloud VNet Resource Group Name: "
read azurespringcloudvnetrg
azurespringcloud_vnet_resource_group_name=$azurespringcloudvnetrg

echo "Enter Azure Spring cloud Spoke VNet : "
read azurespringcloudappspokevnet
azurespringcloudappspokevnet=$azurespringcloudappspokevnet

echo "Enter Azure Spring cloud App SubNet : "
read azurespringcloudappsubnet
azurespringcloud_app_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudappsubnet

echo "Enter Azure Spring cloud Service SubNet : "
read azurespringcloudservicesubnet
azurespringcloud_service_subnet_name='/subscriptions/'$subscription'/resourcegroups/'$azurespringcloud_vnet_resource_group_name'/providers/Microsoft.Network/virtualNetworks/'$azurespringcloudappspokevnet'/subnets/'$azurespringcloudservicesubnet

echo "Enter Azure Log Analytics Workspace Resource Group Name: "
read loganalyticsrg
loganalyticsrg=$loganalyticsrg

echo "Enter Log Analytics Workspace Resource ID: "
read workspace
workspaceID='/subscriptions/'$subscription'/resourcegroups/'$loganalyticsrg'/providers/microsoft.operationalinsights/workspaces/'$workspace

echo "Enter Reserved CIDR Ranges for Azure Spring Cloud: "
read reservedcidrrange
reservedcidrrange=$reservedcidrrange

echo "Enter key=value pair used for tagging Azure Resources (space separated for multiple tags): "
read tag
tags=$tag

randomstring=$(LC_ALL=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 13 | head -n 1)
azurespringcloud_service='spring-'$randomstring #Name of unique Spring Cloud resource
azurespringcloud_appinsights=$azurespringcloud_service
azurespringcloud_resourceid='/subscriptions/'$subscription'/resourceGroups/'$azurespringcloud_resource_group_name'/providers/Microsoft.AppPlatform/Spring/'$azurespringcloud_service

# Create Application Insights
az monitor app-insights component create \
    --app ${azurespringcloud_service} \
    --location ${location} \
    --kind web \
    -g ${azurespringcloudrg} \
    --application-type web \
    --workspace ${workspaceID}

# Create Azure Spring Cloud Instance
az spring-cloud create \
   -n ${azurespringcloud_service} \
   -g ${azurespringcloudrg} \
   -l ${location} \
   --enable-java-agent true \
   --app-insights ${azurespringcloud_service} \
   --sku Standard \
   --app-subnet ${azurespringcloud_app_subnet_name} \
   --service-runtime-subnet ${azurespringcloud_service_subnet_name} \
   --reserved-cidr-range ${reservedcidrrange} \
   --tags ${tags}

# Update diagnostic setting for Azure Spring Cloud instance
az monitor diagnostic-settings create  \
   --name monitoring \
   --resource ${azurespringcloud_resourceid} \
   --logs    '[{"category": "ApplicationConsole","enabled": true}]' \
   --workspace  ${workspaceID}
```

## <a name="deploy-the-cluster"></a>クラスターをデプロイする

Azure CLI スクリプトを使用して Azure Spring Cloud クラスターをデプロイするには、これらの手順に従います。

1. 次のコマンドを使用して Azure にサインインします。

   ```azurecli
   az login
   ```

   サインインすると、このコマンドにより、アクセスできるすべてのサブスクリプションに関する情報が出力されます。 使用するサブスクリプションの名前と ID をメモします。

1. ターゲット サブスクリプションを設定します。

   ```azurecli
   az account set --subscription "<your subscription name>"
   ```

1. Azure Spring Cloud リソース プロバイダーを登録します。

   ```azurecli
   az provider register --namespace 'Microsoft.AppPlatform'
   ```

1. 必要な拡張機能を Azure CLI に追加します。

   ```azurecli
   az extension add --name spring-cloud
   ```

1. 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)」で示されている、Azure Spring Cloud が利用可能なリージョンからデプロイの場所を選択します。

1. 次のコマンドを使用して、Azure の場所の一覧を生成します。 前の手順で選択したリージョンの短い **名前** の値をメモします。

   ```azurecli
   az account list-locations --output table
   ```

1. リソースのデプロイ先となるリソース グループを作成します。

   ```azurecli
   az group create --name <your-resource-group-name> --location <location-name>
   ```

1. [deploySpringCloud.sh](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/CLI/brownfield-deployment/deploySpringCloud.sh) Bash スクリプトをローカルに保存し、Bash プロンプトからそれを実行します。

   ```azurecli
   ./deploySpringCloud.sh
   ```

1. スクリプトから入力を求められたら、次の値を入力します。

   - 先ほど保存した Azure サブスクリプション ID。
   - 先ほど保存した Azure の場所の名前。
   - 先ほど作成したリソース グループの名前。
   - リソースのデプロイ先となる仮想ネットワーク リソース グループの名前。
   - スポーク仮想ネットワークの名前 (*vnet-spoke* など)。
   - Spring Cloud App Service で使用するサブネットの名前 (*snet-app* など)。
   - Spring Cloud ランタイム サービスで使用するサブネットの名前 (*snet-runtime* など)。
   - 診断ログの格納に使用する Azure Log Analytics ワークスペースのリソース グループの名前。
   - Azure Log Analytics ワークスペースの名前 (*la-cb5sqq6574o2a* など)。
   - Azure Spring Cloud で使用する仮想ネットワークからの CIDR 範囲 (*XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16* など)。
   - タグをサポートするすべてのリソースにタグとして適用されるキーと値のペア。 詳細については、「[タグを使用して Azure リソースと整理階層を整理する](../azure-resource-manager/management/tag-resources.md)」を参照してください。 複数のタグを適用するには、スペース区切りのリストを使用します (*environment=Dev BusinessUnit=finance* など)。

この情報を指定すると、スクリプトによって Azure リソースが作成され、デプロイされます。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用してデプロイされたリソースを確認するか、Azure CLI を使用してデプロイされたリソースを一覧表示することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことができます。 不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。 Azure CLI を使用してリソース グループを削除するには、次のコマンドを使用します。

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI を使用して Azure Spring Cloud インスタンスを既存の仮想ネットワークにデプロイし、デプロイを検証しました。 Azure Spring Cloud の詳細については、以下のリソースに進んでください。

- 次のサンプル アプリケーションの 1 つを、次の場所からデプロイします。
   - [MySQL 統合を使用した Pet Clinic アプリ](https://github.com/azure-samples/spring-petclinic-microservices) (MySQL バックエンドを使用したマイクロサービス)。
   - [シンプルな Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- [カスタム ドメイン](tutorial-custom-domain.md)と Azure Spring Cloud を併用します。
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) を使用して Azure Spring Cloud でアプリケーションをインターネットに公開します。
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) に基づいた、セキュリティで保護されたエンドツーエンドの [Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)を参照してください。
