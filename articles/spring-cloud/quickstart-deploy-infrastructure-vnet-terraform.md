---
title: クイックスタート - Terraform を使用して Azure Spring Cloud をプロビジョニングする
description: このクイックスタートでは、Terraform を使用して Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: devx-track-java
ms.author: ariel
ms.date: 11/12/2021
ms.openlocfilehash: 313967fcadf1dac7ec2451b9dc51da1fac9bc435
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488755"
---
# <a name="quickstart-provision-azure-spring-cloud-using-terraform"></a>クイックスタート: Terraform を使用して Azure Spring Cloud をプロビジョニングする

このクイックスタートでは、Terraform を使用して Azure Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。

Azure Spring Cloud では、コードを変更せずに、Spring Boot マイクロサービス アプリケーションを Azure に簡単にデプロイできます。 Spring Cloud アプリケーションのインフラストラクチャはこのサービスによって管理されるので、開発者はコードに専念できます。 Azure Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、ブルー/グリーン デプロイなどを使用して、ライフサイクル管理を提供します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* [Hashicorp Terraform](https://www.terraform.io/downloads.html)
* Azure Spring Cloud クラスター用の 2 つの専用サブネット (1 つはサービス ランタイム用、もう 1 つは Spring Boot マイクロサービス アプリケーション用)。 サブネットと仮想ネットワークの要件については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」セクションを参照してください。
* Azure Spring Cloud の診断設定用の既存の Log Analytics ワークスペースと、ワークスペースベースの Application Insights リソース。 詳細については、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」と「[Azure Spring Cloud での Application Insights Java In-Process Agent](how-to-application-insights.md)」を参照してください。
* Azure Spring Cloud クラスターに使用するために指定した 3 つの内部クラスレス ドメイン間ルーティング (CIDR) の範囲 (それぞれ少なくとも */16*)。 これらの CIDR の範囲は直接ルーティング可能ではなく、Azure Spring Cloud クラスターによって内部的にのみ使用されます。 内部 Spring Cloud の CIDR の範囲に *169.254.0.0/16*、*172.30.0.0/16*、*172.31.0.0/16*、または *192.0.2.0/24* をクラスターに使用することはできません。また、クラスター仮想ネットワークのアドレス範囲に含まれる IP 範囲も使用できません。
* 仮想ネットワークに付与されたサービスのアクセス許可。 仮想ネットワーク上の専用かつ動的なサービス プリンシパルにさらに高度なデプロイやメンテナンスの権限を付与するには、Azure Spring Cloud リソース プロバイダーに仮想ネットワークの所有者としてのアクセス許可が必要です。 手順と詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークにサービス アクセス許可を付与する](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)」セクションを参照してください。
* Azure Firewall またはネットワーク仮想アプライアンス (NVA) を使用している場合は、次の前提条件も満たす必要があります。
   * ネットワークと完全修飾ドメイン名 (FQDN) の規則。 詳細については、「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」を参照してください。
   * サービス ランタイムと Spring Boot マイクロサービス アプリケーションの各サブネットに適用される一意のユーザー定義ルート (UDR)。 UDR の詳細については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。 Spring Cloud クラスターをデプロイする前に、UDR に NVA を宛先とする *0.0.0.0/0* のルートを構成しておく必要があります。 詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[独自のルート テーブルを使用する](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)」セクションを参照してください。

## <a name="review-the-configuration-file"></a>構成ファイルを確認する

このクイックスタートで使用されている構成ファイルは、「[Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)」のものです。

```hcl
provider "azurerm" {
    features {}
}

resource "azurerm_resource_group" "sc_corp_rg" {
    name      = var.resource_group_name
    location  = var.location
}

resource "azurerm_application_insights" "sc_app_insights" {
  name                = var.app_insights_name
  location            = var.location
  resource_group_name = var.resource_group_name
  application_type    = "web"
  depends_on = [azurerm_resource_group.sc_corp_rg]
}

resource "azurerm_spring_cloud_service" "sc" {
  name                = var.sc_service_name
  resource_group_name = var.resource_group_name
  location            = var.location

  network {
    app_subnet_id                   = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.app_subnet_id}"
    service_runtime_subnet_id       = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.Network/virtualNetworks/${var.vnet_spoke_name}/subnets/${var.service_runtime_subnet_id}"
    cidr_ranges                     = var.sc_cidr
  }

  timeouts {
      create = "60m"
      delete = "2h"
  }

  depends_on = [azurerm_resource_group.sc_corp_rg]
  tags = var.tags

}

resource "azurerm_monitor_diagnostic_setting" "sc_diag" {
  name                        = "monitoring"
  target_resource_id          = azurerm_spring_cloud_service.sc.id
  log_analytics_workspace_id = "/subscriptions/${var.subscription}/resourceGroups/${var.azurespringcloudvnetrg}/providers/Microsoft.OperationalInsights/workspaces/${var.sc_law_id}"

  log {
    category = "ApplicationConsole"
    enabled  = true

    retention_policy {
      enabled = false
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = false
    }
  }
}
```

## <a name="apply-the-configuration"></a>構成を適用する

構成を適用するには、次の手順に従います。

1. [variables.tf](https://raw.githubusercontent.com/Azure/azure-spring-cloud-reference-architecture/main/terraform/brownfield-deployment/variable.tf) ファイルをローカルに保存してから、そのファイルをエディターで開きます。

1. ファイルを編集して、次の値を追加します。

   - デプロイ先の Azure アカウントのサブスクリプション ID。

   - 「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud&regions=all)」で示されている、Azure Spring Cloud が利用可能なリージョンからのデプロイの場所。 場所名の短い形式が必要です。 この値を取得するには、次のコマンドを使用して Azure の場所の一覧を生成し、選択したリージョンの **[名前]** の値を検索します。

   ```azurecli
   az account list-locations --output table
   ```

   - デプロイ先のリソース グループの名前。
   - Spring Cloud のデプロイで選択した名前。
   - リソースをデプロイする仮想ネットワーク リソース グループの名前。
   - スポーク仮想ネットワークの名前 (*vnet-spoke* など)。
   - Spring Cloud App Service で使用するサブネットの名前 (*snet-app* など)。
   - Spring Cloud ランタイム サービスで使用するサブネットの名前 (*snet-runtime* など)。
   - Azure Log Analytics ワークスペースの名前。
   - Azure Spring Cloud で使用する仮想ネットワークからの CIDR 範囲 (*XX.X.X.X/16,XX.X.X.X/16,XX.X.X.X/16* など)。
   - タグをサポートするすべてのリソースにタグとして適用されるキーと値のペア。 詳細については、「[タグを使用して Azure リソースと整理階層を整理する](../azure-resource-manager/management/tag-resources.md)」を参照してください。

1. 次のコマンドを実行して、Terraform モジュールを初期化します。

   ```bash
   terraform init
   ```

1. 次のコマンドを実行して、Terraform デプロイ プランを作成します。

   ```bash
   terraform plan -out=springcloud.plan
   ```

1. 次のコマンドを実行して、Terraform デプロイ プランを適用します。

   ```bash
   terraform apply springcloud.plan
   ```

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用してデプロイされたリソースを確認するか、Azure CLI または Azure PowerShell スクリプトを使用してデプロイされたリソースを一覧表示することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことができます。 この記事で作成したリソースが不要になったら、次のコマンドを使用して削除してください。

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Terraform を使用して Azure Spring Cloud インスタンスを既存の仮想ネットワークにデプロイし、デプロイを検証しました。 Azure Spring Cloud の詳細については、以下のリソースに進んでください。

- 次のサンプル アプリケーションの 1 つを、次の場所からデプロイします。
   - [MySQL 統合を使用した Pet Clinic アプリ](https://github.com/azure-samples/spring-petclinic-microservices) (MySQL バックエンドを使用したマイクロサービス)。
   - [シンプルな Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- [カスタム ドメイン](tutorial-custom-domain.md)と Azure Spring Cloud を併用します。
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) を使用して Azure Spring Cloud でアプリケーションをインターネットに公開します。
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) に基づいた、セキュリティで保護されたエンドツーエンドの [Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)を参照してください。
