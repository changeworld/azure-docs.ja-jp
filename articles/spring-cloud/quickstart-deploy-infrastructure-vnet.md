---
title: クイックスタート - Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Spring Cloud をプロビジョニングする
description: このクイックスタートでは、ARM テンプレートを使用し、Spring Cloud クラスターを既存の仮想ネットワークにデプロイする方法について説明します。
services: azure-resource-manager
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-java
ms.author: rhudson
ms.date: 11/12/2021
ms.openlocfilehash: c34d2d9f88f97b80892b4329def4ca84f5f7b70c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488869"
---
# <a name="quickstart-provision-azure-spring-cloud-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure Spring Cloud をプロビジョニングする

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、既存の仮想ネットワークに Azure Spring Cloud クラスターをデプロイする方法について説明します。

Azure Spring Cloud では、コードを変更せずに、Spring Boot マイクロサービス アプリケーションを Azure に簡単にデプロイできます。 Spring Cloud アプリケーションのインフラストラクチャはこのサービスによって管理されるので、開発者はコードに専念できます。 Azure Spring Cloud は、包括的な監視と診断、構成管理、サービス検出、CI/CD 統合、ブルー/グリーン デプロイなどを使用して、ライフサイクル管理を提供します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* Azure Spring Cloud クラスター用の 2 つの専用サブネット (1 つはサービス ランタイム用、もう 1 つは Spring Boot マイクロサービス アプリケーション用)。 サブネットと仮想ネットワークの要件については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」セクションを参照してください。
* Azure Spring Cloud の診断設定用の既存の Log Analytics ワークスペースと、ワークスペースベースの Application Insights リソース。 詳細については、「[診断設定でログとメトリックを分析する](diagnostic-services.md)」と「[Azure Spring Cloud での Application Insights Java In-Process Agent](how-to-application-insights.md)」を参照してください。
* Azure Spring Cloud クラスターに使用するために指定した 3 つの内部クラスレス ドメイン間ルーティング (CIDR) の範囲 (それぞれ少なくとも */16*)。 これらの CIDR の範囲は直接ルーティング可能ではなく、Azure Spring Cloud クラスターによって内部的にのみ使用されます。 内部 Spring Cloud の CIDR の範囲に *169.254.0.0/16*、*172.30.0.0/16*、*172.31.0.0/16*、または *192.0.2.0/24* をクラスターに使用することはできません。また、クラスター仮想ネットワークのアドレス範囲に含まれる IP 範囲も使用できません。
* 仮想ネットワークに付与されたサービスのアクセス許可。 仮想ネットワーク上の専用かつ動的なサービス プリンシパルにさらに高度なデプロイやメンテナンスの権限を付与するには、Azure Spring Cloud リソース プロバイダーに仮想ネットワークの所有者としてのアクセス許可が必要です。 手順と詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[仮想ネットワークにサービス アクセス許可を付与する](how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)」セクションを参照してください。
* Azure Firewall またはネットワーク仮想アプライアンス (NVA) を使用している場合は、次の前提条件も満たす必要があります。

   * ネットワークと完全修飾ドメイン名 (FQDN) の規則。 詳細については、「[仮想ネットワークの要件](how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements)」を参照してください。
   * サービス ランタイムと Spring Boot マイクロサービス アプリケーションの各サブネットに適用される一意のユーザー定義ルート (UDR)。 UDR の詳細については、「[仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。 Spring Cloud クラスターをデプロイする前に、UDR に NVA を宛先とする *0.0.0.0/0* のルートを構成しておく必要があります。 詳細については、「[仮想ネットワークに Azure Spring Cloud をデプロイする](how-to-deploy-in-azure-virtual-network.md)」の「[独自のルート テーブルを使用する](how-to-deploy-in-azure-virtual-network.md#bring-your-own-route-table)」セクションを参照してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されているテンプレートは「[Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)」のものです。

:::code language="json" source="~/azure-spring-cloud-reference-architecture/ARM/brownfield-deployment/azuredeploy.json":::

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [Microsoft.AppPlatform/Spring](/azure/templates/microsoft.appplatform/spring): Azure Spring Cloud インスタンスを作成します。
* [Microsoft.Insights/components](/azure/templates/microsoft.insights/components): Application Insights ワークスペースを作成します。

Azure CLI、Terraform、Bicep のデプロイについては、GitHub 上の [Azure Spring Cloud の参照アーキテクチャ](https://github.com/Azure/azure-spring-cloud-reference-architecture) リポジトリを参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、次の手順に従います。

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートにより、既存の仮想ネットワークに Azure Spring Cloud インスタンスが作成され、既存の Azure Monitor Log Analytics ワークスペースにワークスペースベースの Application Insights インスタンスが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-spring-cloud-reference-architecture%2Fmain%2FARM%2Fbrownfield-deployment%2fazuredeploy.json)

2. 次のフィールドの値を入力します。

   - **リソース グループ**: **[新規作成]** を選択し、**リソース グループ** の一意の名前を入力し、 **[OK]** を選択します。
   - **springCloudInstanceName:** Azure Spring Cloud リソースの名前を入力します。
   - **appInsightsName:** Azure Spring Cloud の Application Insights インスタンスの名前を入力します。
   - **laWorkspaceResourceId:** 既存の Log Analytics ワークスペースのリソース ID を入力します (たとえば、 */subscriptions/\<your subscription>/resourcegroups/\<your log analytics resource group>/providers/Microsoft.OperationalInsights/workspaces/\<your log analytics workspace name>* )。
   - **springCloudAppSubnetID:** Azure Spring Cloud アプリ サブネットの resourceID を入力します。
   - **springCloudRuntimeSubnetID:** Azure Spring Cloud ランタイム サブネットの resourceID を入力します。
   - **springCloudServiceCidrs:** IP アドレス範囲のコンマ区切りリスト (合計 3 つ) を CIDR 形式で入力します。 この IP 範囲は、基盤となる Azure Spring Cloud インフラストラクチャをホストするために予約されています。 これらの 3 つの範囲は、少なくとも */16* の未使用の IP 範囲にするようにします。また、ネットワーク内で使用されているルーティング可能なサブネットの IP 範囲と重複してはなりません。
   - **タグ:** 任意のカスタム タグを入力します。

3. **[確認および作成]** 、 **[作成]** の順に選択します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用してデプロイされたリソースを確認するか、Azure CLI または Azure PowerShell スクリプトを使用してデプロイされたリソースを一覧表示することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことができます。 不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次のコマンドを使用します。

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Azure Spring Cloud インスタンスを既存の仮想ネットワークにデプロイし、デプロイを検証しました。 Azure Spring Cloud と Azure Resource Manager の詳細については、以下のリソースに進んでください。

- 次のサンプル アプリケーションの 1 つを、次の場所からデプロイします。
   - [MySQL 統合を使用した Pet Clinic アプリ](https://github.com/azure-samples/spring-petclinic-microservices) (MySQL バックエンドを使用したマイクロサービス)。
   - [シンプルな Hello World](./quickstart.md?pivots=programming-language-java&tabs=Azure-CLI)。
- [カスタム ドメイン](tutorial-custom-domain.md)と Azure Spring Cloud を併用します。
- [Azure Application Gateway](expose-apps-gateway-azure-firewall.md) を使用して Azure Spring Cloud でアプリケーションをインターネットに公開します。
- [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/) に基づいた、セキュリティで保護されたエンドツーエンドの [Azure Spring Cloud の参照アーキテクチャ](reference-architecture.md)を参照してください。
- [Azure リソース マネージャー](../azure-resource-manager/management/overview.md)の詳細を確認します。
