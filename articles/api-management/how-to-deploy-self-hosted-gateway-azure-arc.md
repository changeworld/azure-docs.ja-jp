---
title: Azure API Management ゲートウェイを Azure Arc にデプロイする
description: Azure Arc を有効にして、セルフホステッド Azure API Management ゲートウェイをデプロイします。
author: dlepow
ms.author: danlep
ms.service: api-management
ms.topic: article
ms.date: 05/25/2021
ms.openlocfilehash: ee2b0fc20111fddd55a987bef4d454d5b63b8e4e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708495"
---
# <a name="deploy-an-azure-api-management-gateway-on-azure-arc-preview"></a>Azure API Management ゲートウェイを Azure Arc にデプロイする (プレビュー)

Azure API Management と [Kubernetes 上の Azure Arc](../azure-arc/kubernetes/overview.md) の統合により、API Management ゲートウェイ コンポーネントを、[Azure Arc 対応 Kubernetes クラスターの拡張機能](../azure-arc/kubernetes/extensions.md)としてデプロイできます。 

API Management ゲートウェイを Azure Arc 対応 Kubernetes クラスターにデプロイすると、ハイブリッドおよびマルチクラウドの各環境のための API Management サポートが拡張されます。 クラスター拡張機能を使用したデプロイを有効にすると、ポリシーの管理と Azure Arc 対応クラスターへの適用を一貫した操作で行うことができます。

[!INCLUDE [preview](./includes/preview/preview-callout-self-hosted-gateway-azure-arc.md)]

> [!NOTE]
> セルフホステッド ゲートウェイを [Kubernetes に直接](./how-to-deploy-self-hosted-gateway-azure-kubernetes-service.md)デプロイすることもできます。

## <a name="prerequisites"></a>前提条件

* [サポートされている Azure Arc リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)内で [Kubernetes クラスターを接続する](../azure-arc/kubernetes/quickstart-connect-cluster.md)。
* `k8s-extension` Azure CLI 拡張機能をインストールする。

    ```azurecli
    az extension add --name k8s-extension
    ```
    `k8s-extension` モジュールが既にインストールされている場合は、最新バージョンに更新する。

    ```azurecli
    az extension update --name k8s-extension
    ```
* [Azure API Management インスタンスを作成する](./get-started-create-service-instance.md)。
* [Azure API Management インスタンスにゲートウェイ リソースをプロビジョニングする](./api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-api-management-gateway-extension-using-azure-cli"></a>Azure CLI を使用して API Management ゲートウェイ拡張機能をデプロイする

1. Azure portal で、API Management インスタンスに移動します。
1. サイド ナビゲーション メニューから **[ゲートウェイ]** を選択します。
1. 一覧からプロビジョニング済みのゲートウェイ リソースを選択して開きます。
1. プロビジョニング済みのゲートウェイ リソースで、サイド ナビゲーション メニューから **[デプロイ]** をクリックします。
1. 次の手順のために、 **[トークン]** と **[構成 URL]** の値をメモします。
1. Azure CLI で、`az k8s-extension create` コマンドを使用してゲートウェイ拡張機能をデプロイします。 `token` と `configuration URL` の値を入力します。
    * 次の例では、`service.type='LoadBalancer'` 拡張機能構成を使用しています。 その他の[使用可能な拡張機能構成](#available-extension-configurations)も参照してください。

    ```azurecli
    az k8s-extension create --cluster-type connectedClusters --cluster-name <cluster-name> \
      --resource-group <rg-name> --name <extension-name> --extension-type Microsoft.ApiManagement.Gateway \
      --scope namespace --target-namespace <namespace> \
      --configuration-settings gateway.endpoint='<Configuration URL>' \
      --configuration-protected-settings gateway.authKey='<token>' \
      --configuration-settings service.type='LoadBalancer' --release-train preview
    ```

    > [!TIP]
    > `authKey` の `-protected-` フラグは省略可能ですが、指定することをお勧めします。 

1. 次の CLI コマンドを使用してデプロイの状態を確認します。
    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name>
    ```
1. **[ゲートウェイ]** の一覧に戻り、ゲートウェイの状態で、ノード数と共に緑色のチェックマークが表示されていることを確認します。 この状態は、デプロイされたセルフホステッド ゲートウェイ ポッドが次の状態であることを示します。
    * API Management サービスと正常に通信している。
    * 定期的に "ハートビート" がある。

## <a name="deploy-the-api-management-gateway-extension-using-azure-portal"></a>Azure portal を使用して API Management ゲートウェイ拡張機能をデプロイする

1. Azure portal で、Azure Arc 接続クラスターに移動します。
1. 左側のメニューで、 **[拡張機能 (プレビュー)]**  >  **[+ 追加]**  >  **[API Management gateway (preview)]\(API Management ゲートウェイ (プレビュー)\)** を選択します。
1. **［作成］** を選択します
1. **[Install API Management gateway]\(API Management ゲートウェイのインストール\)** ウィンドウで、ゲートウェイ拡張機能を構成します。
    * API Management インスタンスのサブスクリプションとリソース グループを選択します。
    * **[Gateway details]\(ゲートウェイの詳細\)** で、**API Management インスタンス** と **ゲートウェイ名** を選択します。 拡張機能の **[名前空間]** スコープを入力し、必要に応じて **[レプリカ]** の数を入力します (お使いの API Management サービス レベルでサポートされている場合)。
    * **[Kubernetes 構成]** で、クラスターの既定の構成または別の構成を選択します。 オプションについては、「[使用可能な拡張機能構成](#available-extension-configurations)」を参照してください。

    :::image type="content" source="./media/how-to-deploy-self-hosted-gateway-azure-arc/deploy-gateway-extension-azure-arc.png" alt-text="Azure portal での拡張機能のデプロイのスクリーンショット":::

1. **[監視]** タブで、必要に応じて監視を有効にして、メトリック追跡要求をゲートウェイとバックエンドにアップロードします。 有効にした場合、既存の **Log Analytics** ワークスペースを選択します。
1. **[Review + install]\(確認とインストール\)** を選択し、 **[インストール]** を選択します。

## <a name="available-extension-configurations"></a>使用可能な拡張機能構成

次の拡張機能構成は **必須** です。

| 設定 | Description |
| ------- | ----------- | 
| `gateway.endpoint` | ゲートウェイ エンドポイントの構成 URL。 |
| `gateway.authKey` | ゲートウェイにアクセスするためのトークン。 | 
| `service.type` | ゲートウェイのための Kubernetes サービス構成: `LoadBalancer`、`NodePort`、または `ClusterIP`。 |

### <a name="log-analytics-settings"></a>Log Analytics の設定

セルフホステッド ゲートウェイの監視を有効にするには、次の Log Analytics 設定を構成します。

| 設定 | Description |
| ------- | ----------- | 
| `monitoring.customResourceId` | API Management インスタンスの Azure Resource Manager リソース ID。 |
| `monitoring.workspaceId` | Log Analytics のワークスペース ID。 | 
| `monitoring.ingestionKey` | Log Analytics からのインジェスト キーを含むシークレット。 |

> [!NOTE]
> Log Analytics を有効にしていない場合: 
> 1. [Log Analytics ワークスペースの作成](../azure-monitor/logs/quick-create-workspace.md)についてのクイックスタートの手順に従います。 
> 1. [Log Analytics エージェントの設定](../azure-monitor/agents/log-analytics-agent.md)を確認する場所について学習します。

## <a name="next-steps"></a>次の手順

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関するページを参照してください。
* [Azure Arc 対応 Kubernetes の拡張機能](../azure-arc/kubernetes/extensions.md)をすべて確認してください。 
* [Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) について詳しく学習してください。
