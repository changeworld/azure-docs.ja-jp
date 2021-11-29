---
title: Azure CLI を使用した Azure Chaos Studio でエージェントベースの障害を使用する実験を作成する
description: Azure CLI を使用してエージェントベースの障害を使用する実験を作成し、カオス エージェントを構成する
author: johnkemnetz
ms.topic: how-to
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: dfb75ea984017a4df298f74210788bd79b6b04e4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714240"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-on-a-virtual-machine-or-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI を使用して仮想マシンまたは仮想マシン スケール セットでエージェントベースの障害を使用するカオス実験を作成する

カオス実験を使用すると、制御された環境でこれらの障害を発生させて、ご自分のアプリケーションが障害に対して回復力を持っていることを検証できます。 このガイドでは、カオス実験と Azure Chaos Studio を使用して、Linux 仮想マシンで高 CPU イベントを発生します。 この実験を実行することで、アプリケーションがリソース不足になることを防ぐことができます。

これらの同じ手順をエージェントベースのあらゆる障害に使用して、実験を設定し、実行することができます。 Azure リソースに対して直接実行し、インストルメンテーションを必要としないサービスダイレクト障害とは異なり、**エージェントベース** の障害には、カオス エージェントのセットアップとインストールが必要です。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- 仮想マシン。 仮想マシンがない場合は、[この手順に従って仮想マシンを作成できます](../virtual-machines/linux/quick-create-portal.md)。
- [お使いの仮想マシンへの SSH 接続](../virtual-machines/ssh-keys-portal.md)を許可するネットワーク セットアップ
- ユーザー割り当てマネージド ID。 ユーザー割り当てマネージド ID をお持ちではない場合は、[次の手順に従って作成](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)できます

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

> [!NOTE]
> これらの手順では、Azure Cloud Shell で Bash ターミナルを使用します。 CLI をローカルまたは PowerShell ターミナルで実行している場合、一部のコマンドは説明のように機能しない場合があります。

## <a name="assign-managed-identity-to-the-virtual-machine"></a>仮想マシンにマネージド ID を割り当てる

仮想マシンで Chaos Studio を設定する前に、`az vm identity assign` または `az vmss identity assign` コマンドを使用して、エージェントのインストールを計画している各仮想マシンまたは仮想マシン スケール セットにユーザー割り当てマネージド ID を割り当てる必要があります。 `$VM_RESOURCE_ID`/`$VMSS_RESOURCE_ID` を、カオスのターゲットとして追加している VM のリソース ID に、`$MANAGED_IDENTITY_RESOURCE_ID` をユーザー割り当てマネージド ID のリソース ID に置き換えます。

**仮想マシン**

```azurecli-interactive
az vm identity assign --ids $VM_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

**Virtual Machine Scale Set**

```azurecli-interactive
az vmss identity assign --ids $VMSS_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

## <a name="enable-chaos-studio-on-your-virtual-machine"></a>仮想マシンで Chaos Studio を有効にする

最初に仮想マシンが Chaos Studio にオンボードされていない限り、Chaos Studio でその仮想マシンに対して障害を挿入することはできません。 仮想マシンを Chaos Studio にオンボードするには、リソースで[ターゲットと機能](chaos-studio-targets-capabilities.md)を作成してから、カオス エージェントをインストールします。 仮想マシン内のターゲットには、サービスダイレクト障害 (エージェントは不要) を可能にするものと、エージェントベース障害 (エージェントのインストールが必要) を可能にするものの 2 種類があります。 カオス エージェントとは、[仮想マシン拡張機能](../virtual-machines/extensions/overview.md)として仮想マシンにインストールするアプリケーションであり、これにより、ゲスト オペレーティング システムに障害を挿入することができます。

### <a name="install-stress-ng-linux-only"></a>stress-ng をインストールする (Linux のみ)

Linux 用の Chaos Studio エージェントには、仮想マシン上でさまざまなストレス イベントを発生させることができるオープンソース アプリケーションの stress-ng が必要です。 stress-ng をインストールするには、[Linux 仮想マシンに接続](../virtual-machines/ssh-keys-portal.md)し、お使いのパッケージ マネージャーに適したインストール コマンドを実行します。次に例を示します。

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

または:

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-and-capabilities"></a>カオスのターゲットと機能を有効にする

次に、各仮想マシンまたは仮想マシン スケール セットに対して、エージェントが Chaos Studio に接続するために使用するユーザー割り当てマネージド ID を指定する Microsoft-Agent ターゲットを設定します。 この例では、すべての VM に対して 1 つのマネージド ID を使用します。 ターゲットは REST API を使用して作成する必要があります。 この例では、`az rest` CLI コマンドを使用して、REST API の呼び出しを実行します。

1. `$USER_IDENTITY_CLIENT_ID` をマネージド ID の clientID (Azure portal の作成したユーザー割り当てマネージド ID の概要で確認できます) に、`$USER_IDENTITY_TENANT_ID` を Azure テナント ID (Azure portal の **[Azure Active Directory]** の **[テナント情報]** の下で確認できます) に置き換えて、以下の JSON を変更します。 Azure CLI を実行しているのと同じ場所に JSON をファイルとして保存します (Cloud Shell で JSON ファイルをドラッグ アンド ドロップしてアップロードできます)。

    ```json
    {
      "properties": {
        "identities": [
          {
            "clientId": "$USER_IDENTITY_CLIENT_ID",
            "tenantId": "$USER_IDENTITY_TENANT_ID",
            "type": "AzureManagedIdentity"
          }
        ]
      }
    }
    ```

2. `$RESOURCE_ID` をターゲット仮想マシンまたは仮想マシン スケール セットのリソース ID に置き換えて、ターゲットを作成します。 `target.json` は、前のステップで作成した JSON ファイルの名前に置き換えます。

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview --body @target.json --query properties.agentProfileId -o tsv
    ```

3. 後の手順で使用するために、このコマンドによって返される **agentProfileId** の GUID をコピーします。

4. 次に示すように、capability.json ファイルを作成して、ターゲットに機能を作成します。 Azure CLI を実行しているのと同じ場所に JSON ファイルを保存します。 `$RESOURCE_ID` をターゲット仮想マシンまたは仮想マシン スケール セットのリソース ID に置き換え、`$CAPABILITY` を[有効にする障害機能の名前](chaos-studio-fault-library.md)に置き換えます。
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    たとえば、CPU 負荷の機能を有効にする場合は、次のようになります。

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/CPUPressure-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

### <a name="install-the-chaos-studio-virtual-machine-extension"></a>Chaos Studio 仮想マシン拡張機能をインストールする

カオス エージェントは、仮想マシンまたは仮想マシン スケール セットのインスタンスで実行され、エージェントベースの障害を実行するアプリケーションです。 インストール中に、エージェントが Chaos Studio を認証するために使用するマネージド ID、作成した Microsoft-Agent ターゲットのプロファイル ID、および必要に応じてエージェントが Azure Application Insights に診断イベントを送信できるようにする Application Insights のインストルメンテーション キーを使用して、エージェントを構成します。

1. 開始する前に、次の詳細を確認してください。
    * **agentProfileId** - ターゲットの作成時に返されるプロパティ。 このプロパティがない場合は、`az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` を実行して、`agentProfileId` プロパティをコピーできます。
    * **ClientId** - ターゲットで使用されるユーザー割り当てマネージド ID のクライアント ID。 このプロパティがない場合は、`az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` を実行して、`clientId` プロパティをコピーできます
    * (省略可能) **AppInsightsKey** - Application Insights コンポーネントのインストルメンテーション キー。ポータルの **[Essentials]** の下にある [Application Insights] ページで確認できます。

2. Chaos Studio VM 拡張機能をインストールします。 `$VM_RESOURCE_ID` を VM のリソース ID に置き換えるか、`$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$VMSS_NAME` を仮想マシン スケール セットのプロパティに置き換えます。 `$AGENT_PROFILE_ID` を agentProfileId に、`$USER_IDENTITY_CLIENT_ID` をマネージド ID の clientID に、`$APP_INSIGHTS_KEY` を Application Insights のインストルメンテーション キーに置き換えます。 Application Insights を使用していない場合は、そのキーと値のペアを削除します。

    #### <a name="install-the-agent-on-a-virtual-machine"></a>仮想マシンにエージェントをインストールする

    **Windows**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    #### <a name="install-the-agent-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットにエージェントをインストールする

    **Windows**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```
3. 仮想マシン スケール セットを設定する場合は、インスタンスが最新のモデルにアップグレードされていることを確認します。 

## <a name="create-an-experiment"></a>実験の作成

仮想マシンがオンボードされ、実験を作成できるようになりました。 カオス実験には、ターゲット リソースに対して実行するアクションを、順番に実行するステップと、並行して実行する分岐に分けて定義します。

1. 以下の JSON サンプルから、実験用の JSON を作成します。 [実験作成用の API](/rest/api/chaosstudio/experiments/create-or-update)と[障害ライブラリ](chaos-studio-fault-library.md)を使用して、実行する実験に対応する JSON を変更します

    ```json
    {
      "identity": {
        "type": "SystemAssigned"
      },
      "location": "centralus",
      "properties": {
        "selectors": [
          {
            "id": "Selector1",
            "targets": [
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myWindowsVM/providers/Microsoft.Chaos/targets/microsoft-agent",
                "type": "ChaosTarget"
              },
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myLinuxVM/providers/Microsoft.Chaos/targets/micosoft-agent",
                "type": "ChaosTarget"
              }
            ],
            "type": "List"
          }
        ],
        "steps": [
          {
            "branches": [
              {
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ],
                "name": "Branch 1"
              }
            ],
            "name": "Step 1"
          }
        ]
      }
    }
    ```

    仮想マシン スケール セットに対して実行している場合は、ターゲットにするインスタンス番号を含むように障害パラメーターを変更します。

    ```json
    "parameters": [
      {
        "key": "pressureLevel",
        "value": "95"
      },
      {
        "key": "virtualMachineScaleSetInstances",
        "value": "[0,1,2]"
      }
    ]
    ```

    Azure portal でスケール セットのインスタンス番号を特定するには、仮想マシン スケール セットに移動し、 **[インスタンス]** をクリックします。 インスタンス名は、インスタンス番号で終わります。
    
2. `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$EXPERIMENT_NAME` を実験のプロパティに置き換えて、Azure CLI を使用して実験を作成します。 実験用の JSON を保存してアップロードし、`experiment.json` を JSON ファイル名で更新したことを確認します。

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    実験ごとに、対応するシステム割り当てマネージド ID が作成されます。 次のステップの応答でこれが識別できるように、`principalId` をメモします。

## <a name="give-experiment-permission-to-your-virtual-machine"></a>仮想マシンに実験アクセス許可を付与する
カオス実験を作成すると、ターゲット リソースに対して障害を実行するシステム割り当てマネージド ID が Chaos Studio によって作成されます。 実験を正常に実行するには、この ID にターゲット リソースへの[適切なアクセス許可](chaos-studio-fault-providers.md)を付与する必要があります。 エージェントベースの障害には、閲覧者ロールが必要です。 仮想マシンの共同作成者など、*/読み取りアクセス許可を持たない他のロールでは、エージェントベースの障害に対する適切なアクセス許可が付与されません。

次のコマンドを使用して、仮想マシンまたは仮想マシン スケール セットへのアクセスを実験に付与します。`$EXPERIMENT_PRINCIPAL_ID` は、前のステップの principalId に、`$RESOURCE_ID` はターゲット仮想マシンまたは仮想マシン スケール セットのリソース ID (実験定義で使用されるカオス エージェントのリソース ID ではなく、VM のリソース ID) に置き換えます。 実験でターゲットとなる仮想マシンまたは仮想マシン スケール セットごとに、このコマンドを実行します。

```azurecli-interactive
az role assignment create --role "Reader" --assignee-principal-type "ServicePrincipal" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID 
```


## <a name="run-your-experiment"></a>実験を実行する
これで、実験を実行する準備が整いました。 影響を確認するため、[Azure Monitor メトリック グラフ](../azure-monitor/essentials/tutorial-metrics.md)を別のブラウザー タブで開いて、お使いの仮想マシンの CPU 負荷を表示することをお勧めします。

1. `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$EXPERIMENT_NAME` を実験のプロパティに置き換えて、Azure CLI を使用して実験を開始します。

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 応答には、実験の実行時に実験の状態を照会するために使用できる状態 URL が含まれています。

## <a name="next-steps"></a>次のステップ
エージェントベースの実験を実行したので、次の準備が整いました。
- [サービスダイレクト障害を使用する実験を作成する](chaos-studio-tutorial-service-direct-portal.md)
- [実験を管理する](chaos-studio-run-experiment.md)
