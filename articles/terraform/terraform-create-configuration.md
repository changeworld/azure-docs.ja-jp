---
title: クイックスタート - Azure 用の Terraform 構成の作成
description: Azure Cosmos DB と Azure Container Instances をデプロイして、Azure 上の Terraform の使用を開始します。
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470023"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>クイック スタート:Azure 用の Terraform 構成の作成

このクイックスタートでは、Terraform を使用して Azure リソースを作成する方法について説明します。 このアーティクルの手順では、次のリソースを作成する方法について説明します。

> [!div class="checklist"]
> * Azure Cosmos DB インスタンス
> * Azure Container Instances
> * これら 2 つのリソース間で動作するアプリ

## <a name="create-first-configuration"></a>最初の構成を作成する

このセクションでは、Azure Cosmos DB インスタンスの構成を作成します。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. Azure Cloud Shell を開きます。

1. Cloud Shell エディターを開始します。

    ```bash
    code main.tf
    ```

1. この手順の構成では、いくつかの Azure リソースをモデル化します。 これらのリソースには、Azure リソース グループと Azure Cosmos DB インスタンスが含まれます。 ランダム整数は、固有の Cosmos DB インスタンス名の作成に使用されます。 いくつかの Cosmos DB 設定も構成されています。 詳しくは、「[Cosmos DB Terraform のリファレンス](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html)」をご覧ください。 次の Terraform 構成をコピーしてエディターに貼り付けます。

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

## <a name="run-the-configuration"></a>構成を実行する

このセクションでは、いくつかの Terraform コマンドを使用して構成を実行します。

1. [terraform init](https://www.terraform.io/docs/commands/init.html) コマンドを実行すると、作業ディレクトリが初期化されます。 Cloud Shell で次のコマンドを実行します。

    ```bash
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) コマンドを使用して、構成構文を検証できます。 `-out` パラメーターは、結果をファイルに送信します。 出力ファイルは、後で構成を適用するために使用できます。 Cloud Shell で次のコマンドを実行します。

    ```bash
    terraform plan --out plan.out
    ```

1. 構成を適用するには、[terraform apply](https://www.terraform.io/docs/commands/apply.html) コマンドを使用します。 前の手順の出力ファイルが指定されています。 このコマンドにより、Azure リソースが作成されます。 Cloud Shell で次のコマンドを実行します。

    ```bash
    terraform apply plan.out
    ```

1. Azure portal 内の結果を確認するには、新しいリソース グループを参照します。 新しい Azure Cosmos DB インスタンスは、新しいリソース グループにあります。

## <a name="update-configuration"></a>構成を更新する

このセクションでは、Azure コンテナー インスタンスを含めるように構成を更新する方法を説明します。 コンテナーは、Cosmos DB のデータを読み書きするアプリケーションを実行します。

1. Cloud Shell エディターを開始します。

    ```bash
    code main.tf
    ```

1. この手順の構成では、`COSMOS_DB_ENDPOINT` と `COSMOS_DB_MASTERKEY` の 2 つの環境変数を設定します。 これらの変数には、データベースにアクセスするための場所とキーが保持されます。 これらの変数の値は、前の手順で作成されたデータベース インスタンスから取得されます。 このプロセスは、補間と呼ばれます。 Terraform の補間の詳細については、「[Interpolation Syntax (補間の構文)](https://www.terraform.io/docs/configuration/interpolation.html)」を参照してください。 この構成には、コンテナー インスタンスの完全修飾ドメイン名 (FQDN) を返す出力ブロックも含まれています。 以下のコードをコピーして、エディターに貼り付けます。

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

1. 前のセクションで行ったように、変更内容を表示するには、次のコマンドを実行します。

    ```bash
    terraform plan --out plan.out
    ```

1. `terraform apply` コマンドを実行して構成を適用します。

    ```bash
    terraform apply plan.out
    ```

1. コンテナー インスタンスの FQDN をメモします。

## <a name="test-application"></a>アプリケーションをテストする

アプリケーションをテストするには、コンテナー インスタンスの FQDN に移動します。 次の出力のような結果が表示されます。

![Azure 投票アプリケーション](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要になったら、削除してください。

このチュートリアルで作成した Azure リソースを削除するには、[terraform destroy](https://www.terraform.io/docs/commands/destroy.html) コマンドを実行します。

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure リソースを作成するために Terraform をインストールして構成する](terraform-install-configure.md)。