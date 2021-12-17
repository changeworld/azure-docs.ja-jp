---
title: クイックスタート - Bicep を使用して Azure Spring Cloud をプロビジョニングする
description: このクイックスタートでは、Bicep を使用して Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ssarwa
ms.date: 11/12/2021
ms.openlocfilehash: ac155d1937638b4be26c74214d63d35edd009322
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488813"
---
# <a name="quickstart-provision-azure-spring-cloud-using-bicep"></a>クイックスタート: Bicep を使用して Azure Spring Cloud をプロビジョニングする

このクイックスタートでは、Bicep テンプレートを使用して Azure Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。

Azure Spring Cloud では、コードを変更せずに、Spring Boot マイクロサービス アプリケーションを Azure に簡単にデプロイできます。 Spring Cloud アプリケーションのインフラストラクチャはこのサービスによって管理されるので、開発者はコードに専念できます。 Azure Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、ブルー/グリーン デプロイなどを使用して、ライフサイクル管理を提供します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* Azure Spring Cloud クラスター用の 2 つの専用サブネット (1 つはサービス ランタイム用、もう 1 つは Spring Boot マイクロサービス アプリケーション用)。 サブネットと仮想ネットワークの要件については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」セクションを参照してください。
* Azure Spring Cloud の診断設定用の既存の Log Analytics ワークスペース。 詳しくは、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」をご覧ください。
* Azure Spring Cloud クラスターに使用するために指定した 3 つの内部クラスレス ドメイン間ルーティング (CIDR) の範囲 (それぞれ少なくとも */16*)。 これらの CIDR の範囲は直接ルーティング可能ではなく、Azure Spring Cloud クラスターによって内部的にのみ使用されます。 内部 Spring Cloud の CIDR の範囲に *169.254.0.0/16*、*172.30.0.0/16*、*172.31.0.0/16*、または *192.0.2.0/24* をクラスターに使用することはできません。また、クラスター仮想ネットワークのアドレス範囲に含まれる IP 範囲も使用できません。
* 仮想ネットワークに付与されたサービスのアクセス許可。 仮想ネットワーク上の専用かつ動的なサービス プリンシパルにさらに高度なデプロイやメンテナンスの権限を付与するには、Azure Spring Cloud リソース プロバイダーに仮想ネットワークの所有者としてのアクセス許可が必要です。 手順と詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークにサービス アクセス許可を付与する](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)」セクションを参照してください。
* Azure Firewall またはネットワーク仮想アプライアンス (NVA) を使用している場合は、次の前提条件も満たす必要があります。
   * ネットワークと完全修飾ドメイン名 (FQDN) の規則。 詳細については、「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」を参照してください。
   * サービス ランタイムと Spring Boot マイクロサービス アプリケーションの各サブネットに適用される一意のユーザー定義ルート (UDR)。 UDR の詳細については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。 Spring Cloud クラスターをデプロイする前に、UDR に NVA を宛先とする *0.0.0.0/0* のルートを構成しておく必要があります。 詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[独自のルート テーブルを使用する](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)」セクションを参照してください。
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="deploy-using-bicep"></a>Bicep を使用したデプロイ

クラスターをデプロイするには、次の手順に従います。

1. 次の内容で *azuredeploy.bicep* ファイルを作成します。

   ```Bicep
   @description('The instance name of the Azure Spring Cloud resource')
   param springCloudInstanceName string

   @description('The name of the Application Insights instance for Azure Spring Cloud')
   param appInsightsName string

   @description('The resource ID of the existing Log Analytics workspace. This will be used for both diagnostics logs and Application    Insights')
   param laWorkspaceResourceId string

   @description('The resourceID of the Azure Spring Cloud App Subnet')
   param springCloudAppSubnetID string

   @description('The resourceID of the Azure Spring Cloud Runtime Subnet')
   param springCloudRuntimeSubnetID string

   @description('Comma-separated list of IP address ranges in CIDR format. The IP ranges are reserved to host underlying Azure Spring Cloud    infrastructure, which should be 3 at least /16 unused IP ranges, must not overlap with any Subnet IP ranges')
   param springCloudServiceCidrs string = '10.0.0.0/16,10.2.0.0/16,10.3.0.1/16'

   @description('The tags that will be associated to the Resources')
   param tags object = {
     environment: 'lab'
   }

   var springCloudSkuName = 'S0'
   var springCloudSkuTier = 'Standard'
   var location = resourceGroup().location

   resource appInsights 'Microsoft.Insights/components@2020-02-02-preview' = {
     name: appInsightsName
     location: location
     kind: 'web'
     tags: tags
     properties: {
       Application_Type: 'web'
       Flow_Type: 'Bluefield'
       Request_Source: 'rest'
       WorkspaceResourceId: laWorkspaceResourceId
     }
   }

   resource springCloudInstance 'Microsoft.AppPlatform/Spring@2020-07-01' = {
     name: springCloudInstanceName
     location: location
     tags: tags
     sku: {
       name: springCloudSkuName
       tier: springCloudSkuTier
     }
     properties: {
       networkProfile: {
         serviceCidr: springCloudServiceCidrs
         serviceRuntimeSubnetId: springCloudRuntimeSubnetID
         appSubnetId: springCloudAppSubnetID
       }
     }
   }

   resource springCloudMonitoringSettings 'Microsoft.AppPlatform/Spring/monitoringSettings@2020-07-01' = {
     name: '${springCloudInstance.name}/default'
     properties: {
       traceEnabled: true
       appInsightsInstrumentationKey: appInsights.properties.InstrumentationKey
     }
   }

   resource springCloudDiagnostics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
     name: 'monitoring'
     scope: springCloudInstance
     properties: {
       workspaceId: laWorkspaceResourceId
       logs: [
         {
           category: 'ApplicationConsole'
           enabled: true
           retentionPolicy: {
             days: 30
             enabled: false
           }
         }
       ]
     }
   }
   ```

1. Bash ウィンドウを開き、次の Azure CLI コマンドを実行します。 *\<value>* プレースホルダーは次の値に置き換えます。

   - **resource-group:** Spring Cloud インスタンスをデプロイするためのリソース グループ名。
   - **springCloudInstanceName:** Azure Spring Cloud リソースの名前。
   - **appInsightsName:** Azure Spring Cloud の Application Insights インスタンスの名前。
   - **laWorkspaceResourceId:** 既存の Log Analytics ワークスペースのリソース ID (たとえば、 */   subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/   Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* )。
   - **springCloudAppSubnetID:** Azure Spring Cloud アプリ サブネットの resourceID。
   - **springCloudRuntimeSubnetID:** Azure Spring Cloud ランタイム サブネットの resourceID。
   - **springCloudServiceCidrs:** CIDR 形式での IP アドレス範囲のコンマ区切りリスト (合計 3 つ)。 この IP 範囲は、基盤となる Azure Spring Cloud インフラストラクチャをホストするために予約されています。 これらの 3 つの範囲は、少なくとも */16* の未使用の IP 範囲にするようにします。また、ネットワーク内で使用されているルーティング可能なサブネットの IP 範囲と重複してはなりません。

   ```azurecli
   az deployment group create \
       --resource-group <value> \
       --name initial \
       --template-file azuredeploy.bicep \
       --parameters \
           springCloudInstanceName=<value> \
           appInsightsName=<value> \
           laWorkspaceResourceId=<value> \
           springCloudAppSubnetID=<value> \
           springCloudRuntimeSubnetID=<value> \
           springCloudServiceCidrs=<value>
   ```

   このコマンドでは、Bicep テンプレートを使用して、既存の仮想ネットワークに Azure Spring Cloud インスタンスを作成します。 また、このコマンドによって、既存の Azure Monitor Log Analytics ワークスペースにワークスペースベースの Application Insights インスタンスも作成されます。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用してデプロイされたリソースを確認するか、Azure CLI または Azure PowerShell スクリプトを使用してデプロイされたリソースを一覧表示することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことができます。 不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。 Azure CLI を使用してリソース グループを削除するには、次のコマンドを使用します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Bicep を使用して Azure Spring Cloud インスタンスを既存の仮想ネットワークにデプロイし、デプロイを検証しました。 Azure Spring Cloud の詳細については、以下のリソースに進んでください。

- 次のサンプル アプリケーションの 1 つを、次の場所からデプロイします。
   - [MySQL 統合を使用した Pet Clinic アプリ](https://github.com/azure-samples/spring-petclinic-microservices) (MySQL バックエンドを使用したマイクロサービス)。
   - [シンプルな Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- [カスタム ドメイン](tutorial-custom-domain.md)と Azure Spring Cloud を併用します。
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) を使用して Azure Spring Cloud でアプリケーションをインターネットに公開します。
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) に基づいた、セキュリティで保護されたエンドツーエンドの [Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)を参照してください。
