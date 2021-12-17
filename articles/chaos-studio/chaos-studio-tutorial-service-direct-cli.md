---
title: Azure CLI で Azure Chaos Studio を使い、サービスダイレクト障害を使う実験を作成する
description: Azure CLI でサービスダイレクト障害を使う実験を作成する
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e6f1f215a96b6f651e344087c98a3a7d9be278db
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718528"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-with-the-azure-cli"></a>Azure CLI でサービスダイレクト障害を使うカオス実験を作成する

カオス実験を使用して、制御された環境で障害を引き起こすことで、アプリケーションにこれらの障害に対する回復性があることを確認できます。 このガイドでは、カオス実験と Azure Chaos Studio を使用して、マルチ読み取り、単一書き込みの Azure Comos DB フェールオーバーを実行します。 この実験を行うことで、フェールオーバー イベントが発生したときにデータ損失を防ぐことができます。

これと同じ手順を使用して、サービスダイレクト障害に対する実験を設定して実行できます。 カオス エージェントのインストールが必要なエージェントベースの障害とは異なり、**サービスダイレクト** 障害は、インストルメンテーションを必要とせずに Azure リソースに対して直接実行されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Azure Cosmos DB アカウント。 Azure Cosmos DB アカウントがない場合は、[こちらの手順に従って作成](../cosmos-db/sql/create-cosmosdb-resources-portal.md)できます。
- Azure Cosmos DB アカウントの読み取りリージョンと書き込みリージョンが 1 つ以上設定されていること。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

> [!NOTE]
> これらの手順では、Azure Cloud Shell で Bash ターミナルを使用します。 CLI をローカルまたは PowerShell ターミナルで実行している場合、一部のコマンドは説明のように機能しない場合があります。

## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントで Chaos Studio を有効化する

Chaos Studio では、最初にリソースが Chaos Studio にオンボードされていない限り、リソースに対してフォールトを挿入することはできません。 リソースに[ターゲットと機能](chaos-studio-targets-capabilities.md)を作成することによって、リソースを Chaos Studio にオンボードします。 Azure Cosmos DB アカウントには 1 つのターゲットの種類 (サービスダイレクト) と 1 つの機能 (フェールオーバー) のみがありますが、その他のリソースには最大 2 つのターゲットの種類 (サービスダイレクト障害とエージェントベースの障害に各 1 つ) と多くの機能を含めることができます。

1. `$RESOURCE_ID` をオンボードするリソースのリソース ID に、`$TARGET_TYPE` を[オンボードするターゲットの種類](chaos-studio-fault-providers.md)に置き換えて、ターゲットを作成します。

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    たとえば、仮想マシンをサービスダイレクト ターゲットとしてオンボードする場合:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. `$RESOURCE_ID` をオンボードするリソースのリソース ID に、`$TARGET_TYPE` を[オンボードするターゲットの種類](chaos-studio-fault-providers.md)に、`$CAPABILITY` を[有効にする障害機能の名前](chaos-studio-fault-library.md)に置き換えて、ターゲット上に機能を作成します。
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    たとえば、仮想マシンのシャットダウン機能を有効にする場合:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

これで、Azure Cosmos DB アカウントが Chaos Studio に正常にオンボードしました。

## <a name="create-an-experiment"></a>実験の作成
これで Azure Cosmos DB アカウントがオンボードされたので、実験を作成できます。 カオス実験には、ターゲット リソースに対して実行するアクションを、順番に実行するステップと、並行して実行する分岐に分けて定義します。

1. 以下の JSON サンプルから、実験用の JSON を作成します。 [実験作成用の API](/rest/api/chaosstudio/experiments/create-or-update) と[障害ライブラリ](chaos-studio-fault-library.md)を使って、実行する実験に対応する JSON を変更します

    ```json
    {
      "location": "eastus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                        "key": "readRegion",
                        "value": "East US 2"
                      }
                    ],
                    "name": "urn:csci:microsoft:cosmosDB:failover/1.0"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$EXPERIMENT_NAME` を実験のプロパティに置き換えて、Azure CLI を使って実験を作成します。 実験用の JSON を保存してアップロードし、`experiment.json` を JSON ファイル名で更新したことを確認します。

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    実験ごとに、対応するシステム割り当てマネージド ID が作成されます。 次のステップの応答でこれが識別できるように、`principalId` をメモします。

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントに実験アクセス許可を付与する
カオス実験を作成すると、ターゲット リソースに対して障害を実行するシステム割り当てマネージド ID が Chaos Studio によって作成されます。 実験を正常に実行するには、この ID にターゲット リソースへの[適切なアクセス許可](chaos-studio-fault-providers.md)を付与する必要があります。

`$EXPERIMENT_PRINCIPAL_ID` を前の手順の principalId に置き換え、`$RESOURCE_ID` をターゲット リソースのリソース ID (この場合は Cosmos DB インスタンスのリソース ID) に置き換え、次のコマンドを使い、実験にリソースへのアクセスを許可します。 そのロールを、[そのリソースの種類に適した組み込みロール](chaos-studio-fault-providers.md)に変更します。 実験の対象となるリソースごとに、このコマンドを実行します。 

```azurecli-interactive
az role assignment create --role "Cosmos DB Operator" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>実験を実行する
これで、実験を実行する準備が整いました。 影響を確認するには、Azure Cosmos DB アカウントの概要を開き、別のブラウザー タブで **データをグローバルにレプリケートする** ことをお勧めします。実験中に定期的に更新すると、リージョンのスワップが表示されます。

1. `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$EXPERIMENT_NAME` を実験のプロパティに置き換え、Azure CLI を使って実験を開始します。

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 応答には、実験の実行時に実験の状態を照会するために使用できる状態 URL が含まれています。

## <a name="next-steps"></a>次の手順
これで、Azure Cosmos DB サービスダイレクト実験を実行したので、次のことを行う準備ができました。
- [エージェントベースの障害を使用する実験を作成する](chaos-studio-tutorial-agent-based-portal.md)
- [実験を管理する](chaos-studio-run-experiment.md)
