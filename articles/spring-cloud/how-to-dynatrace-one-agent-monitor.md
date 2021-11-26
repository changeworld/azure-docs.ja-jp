---
title: Dynatrace Java OneAgent で Spring Boot アプリを監視する方法
description: Dynatrace Java OneAgent を使って Azure Spring Cloud の Spring Boot アプリを監視する方法
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 4e84cb9d33e850ed3148c41cff25c3fe17a93ae8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486253"
---
# <a name="how-to-monitor-spring-boot-apps-with-dynatrace-java-oneagent"></a>Dynatrace Java OneAgent で Spring Boot アプリを監視する方法

この記事では Dynatrace Java OneAgent を使って Azure Spring Cloud の Spring Boot アプリを監視する方法について説明します。


Dynatrace OneAgent を使用すると、次のことができます。

* Dynatrace OneAgent を使用してアプリを監視する。
* 環境変数を使用して Dynatrace OneAgent を構成する。
* Dynatrace ダッシュボードからすべての監視データを確認する。

次のビデオでは、Dynatrace OneAgent を紹介しています。

<br>

> [!VIDEO https://www.youtube.com/embed/PF0_SxuiZ2w]

## <a name="prerequisites"></a>前提条件

* [Azure CLI](/cli/azure/install-azure-cli)
* [Dynatrace アカウント](https://www.dynatrace.com/)
* [Dynatrace PaaS トークンとテナント トークン](https://www.dynatrace.com/support/help/reference/dynatrace-concepts/access-tokens/)

## <a name="activate-dynatrace-oneagent"></a>Dynatrace OneAgent のアクティブ化

次のセクションでは、Dynatrace OneAgent をアクティブ化する方法について説明します。

### <a name="prepare-your-azure-spring-cloud-environment"></a>Azure Spring Cloud の環境を準備する

1. Azure Spring Cloud のインスタンスを作成します。
1. 次のコマンドを実行して、Dynatrace に報告するアプリケーションを作成します。 プレースホルダー *\<...>* を実際の値に置き換えてください。
   ```azurecli
   az spring-cloud app create \
       --resource-group <your-resource-group-name> \
       --service <your-Azure-Spring-Cloud-name> \
       --name <your-application-name> \
       --is-public true 
   ```

### <a name="determine-the-values-for-the-required-environment-variables"></a>必要な環境変数の値を決定する

Azure Spring Cloud インスタンスで Dynatrace OneAgent をアクティブ化するには、`DT_TENANT`、`DT_TENANTTOKEN`、`DT_CONNECTION_POINT`、`DT_CLUSTER_ID` の 4 つの環境変数を構成する必要があります。 詳細については、[OneAgent と Azure Spring Cloud の統合](https://www.dynatrace.com/support/help/shortlink/azure-spring)に関する記事を参照してください。

複数のインスタンスを持つアプリケーションの場合、Dynatrace ではいくつかの方法でそれらをグループ化できます。 `DT_CLUSTER_ID` はその方法の 1 つです。 詳細については、「[プロセス グループの構造のカスタマイズ](https://www.dynatrace.com/support/help/how-to-use-dynatrace/process-groups/configuration/adapt-the-composition-of-default-process-groups/)」を参照してください。

### <a name="add-the-environment-variables-to-your-application"></a>アプリケーションに環境変数を追加する

Azure portal または Azure CLI のいずれかを使用して、環境変数のキーと値のペアをアプリケーションに追加できます。

#### <a name="option-1-azure-cli"></a>オプション 1: Azure CLI

Azure CLI を使用してキーと値のペアを追加するには、次のコマンドを実行します。プレースホルダー *\<...>* は、前の手順で特定した値に置き換えてください。

```azurecli
az spring-cloud app deploy \
    --resource-group <your-resource-group-name> \
    --service <your-Azure-Spring-Cloud-name> \
    --name <your-application-name> \
    --jar-path app.jar \
    --env \
        DT_TENANT=<your-environment-ID> \
        DT_TENANTTOKEN=<your-tenant-token> \
        DT_CONNECTION_POINT=<your-communication-endpoint>
```

#### <a name="option-2-portal"></a>オプション 2: ポータル

Azure portal を使用してキーと値のペアを追加するには、次の手順に従います。

1. 既存のアプリケーションの一覧に移動します。

   :::image type="content" source="media/dynatrace-oneagent/existing-applications.png" alt-text="Azure Spring Cloud の [アプリ] セクションが表示されている Azure portal のスクリーンショット。" lightbox="media/dynatrace-oneagent/existing-applications.png":::

1. アプリケーションを選択して、アプリケーションの **[概要]** ページに移動します。

   :::image type="content" source="media/dynatrace-oneagent/overview-application.png" alt-text="アプリケーションの [概要] セクションのスクリーンショット。" lightbox="media/dynatrace-oneagent/overview-application.png":::

1. **[構成]** を選択して、アプリケーションの **[環境変数]** セクションで値を追加、更新、または削除します。

   :::image type="content" source="media/dynatrace-oneagent/configuration-application.png" alt-text="アプリケーションの [構成] セクションの [環境変数] タブのスクリーンショット。" lightbox="media/dynatrace-oneagent/configuration-application.png":::

## <a name="automate-provisioning"></a>プロビジョニングを自動化する

Terraform または Azure Resource Manager テンプレート (ARM テンプレート) を使用すると、プロビジョニング オートメーション パイプラインを実行することもできます。 このパイプラインでは、作成およびデプロイする新しいアプリケーションをインストルメント化して監視するための完全なハンズオン エクスペリエンスが提供されます。

### <a name="automate-provisioning-using-terraform"></a>Terraform でプロビジョニングを自動化する

Terraform テンプレートで環境変数を構成するには、次のコードをテンプレートに追加し、 *\<...>* プレースホルダーを実際の値に置き換えます。 詳細については、「[Azure Spring Cloud のアクティブなデプロイを管理する](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/spring_cloud_active_deployment)」を参照してください。

```terraform
environment_variables = {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

### <a name="automate-provisioning-using-an-arm-template"></a>ARM テンプレートでプロビジョニングを自動化する

ARM テンプレートで環境変数を構成するには、次のコードをテンプレートに追加し、 *\<...>* プレースホルダーを実際の値に置き換えます。 詳細については、[Microsoft.AppPlatform Spring/apps/deployments](/azure/templates/microsoft.appplatform/spring/apps/deployments?tabs=json) を参照してください。

```ARM template
"environmentVariables": {
  "DT_TENANT": "<your-environment-ID>",
  "DT_TENANTTOKEN": "<your-tenant-token>",
  "DT_CONNECTION_POINT": "<your-communication-endpoint>",
  "DT_CLUSTER_ID": "<your-cluster-ID>"
}
```

## <a name="view-reports-in-dynatrace"></a>Dynatrace でレポートを表示する

このセクションでは、Dynatrace でさまざまなレポートを見つける方法について説明します。

> [!NOTE]
> Dynatrace のメニューとユーザー インターフェイスは徐々に発展しています。 このため、ダッシュボードが Dynatrace Web サイト内の他のセクションに移動され、次のスクリーンショットに現在のバージョンのユーザー インターフェイスが反映されていない場合があります。

環境変数をアプリケーションに追加すると、Dynatrace でデータの収集が開始されます。 レポートを表示するには、[Dynatrace メニュー](https://www.dynatrace.com/support/help/get-started/navigation/)を使用して **[サービス]** に移動し、アプリケーションを選択します。

**[サービス フロー]** は **\<your-app-name>/[詳細]/[サービス フロー]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png" alt-text="Dynatrace の [サービス フロー] レポートのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-app-flow.png":::

**[メソッドのホットスポット]** は **\<your-app-name>/[詳細]/[メソッドのホットスポット]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png" alt-text="[メソッドのホットスポット] レポートのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-hotspots.png":::

**[データベース ステートメント]** は **\<your-app-name>/[詳細]/[応答時間分析]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png" alt-text="[応答時間分析] レポートとデータベース ステートメント セクションのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-contribution.png":::

次に、 **[多次元分析]** セクションに移動します。

**[上位のデータベース ステートメント]** は、 **[多次元分析]/[上位のデータベース ステートメント]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png" alt-text="[上位のデータベース ステートメント] レポートのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-top-database.png":::

**[例外の概要]** は、 **[多次元分析]/[例外の概要]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png" alt-text="[例外の概要] レポートのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-exception-analysis.png":::

次に、 **[プロファイルと最適化]** セクションに移動します。

**[CPU の分析]** は、 **[プロファイルと最適化]/[CPU の分析]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png" alt-text="[CPU の分析] レポートのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-cpu-analysis.png":::

次に、 **[データベース]** セクションに移動します。

**[バックトレース]** は、 **[データベース]/[詳細]/[バックトレース]** から見つけることができます。

:::image type="content" source="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png" alt-text="[バックトレース] レポートのスクリーンショット。" lightbox="media/dynatrace-oneagent/spring-cloud-dynatrace-database-backtrace.png":::

## <a name="view-dynatrace-oneagent-logs"></a>Dynatrace OneAgent のログを見る

Azure Spring Cloud では、既定で Dynatrace OneAgent の "*情報*" レベルのログが `STDOUT` に出力されます。 このログは、アプリケーション ログと混在しています。 アプリケーション ログから明示的なエージェント バージョンを見つけることができます。

Dynatrace エージェントのログは、次の場所から取得することもできます。

* Azure Spring Cloud のログ
* Azure Spring Cloud Application Insights
* Azure Spring Cloud LogStream

Dynatrace によって提供されるいくつかの環境変数を適用すると、Dynatrace OneAgent のログ記録を構成することができます。 たとえば、`DT_LOGLEVELCON` によってログのレベルが制御されます。

> [!CAUTION]
> Dynatrace 用に Azure Spring Cloud によって提供されている既定のログ記録の動作をオーバーライドしないことを強くお勧めします。 オーバーライドした場合、上記のログ記録のシナリオがブロックされ、ログ ファイルが失われる可能性があります。 たとえば、`DT_LOGLEVELFILE` 環境変数をアプリケーションに出力しないでください。

## <a name="dynatrace-oneagent-upgrade"></a>Dynatrace OneAgent のアップグレード

Dynatrace OneAgent の自動アップグレードは無効になり、四半期ごとに JDK を使用してアップグレードされます。 エージェントのアップグレードは、次のシナリオに影響する場合があります。

* アップグレード前に Dynatrace OneAgent が使用されていた既存のアプリケーションは変更されませんが、新しいバージョンの Dynatrace OneAgent を利用するには再起動または再デプロイが必要になります。
* アップグレード後に作成されたアプリケーションでは、新しいバージョンの Dynatrace OneAgent が使用されます。

## <a name="vnet-injection-instance-outbound-traffic-configuration"></a>VNet インジェクション インスタンスの送信トラフィックの構成

Azure Spring Cloud の VNet インジェクション インスタンスの場合、Dynatrace OneAgent に対して Dynatrace 通信エンドポイントの送信トラフィックが正しく構成されていることを確認する必要があります。 `communicationEndpoints` を取得する方法の詳細については、「[Deployment API - OneAgent の接続情報の取得](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/deployment/oneagent/get-connectivity-info/)」を参照してください。 詳細については、「[VNET での Azure Spring Cloud の実行に関するお客様の責任](vnet-customer-responsibilities.md)」を参照してください。

## <a name="dynatrace-support-model"></a>Dynatrace サポート モデル

アプリケーション専用モードで Dynatrace OneAgent をデプロイするときの制限事項については、[OneAgent のプラットフォームと機能のサポートのマトリックス](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix)の[クラウド アプリケーションのプラットフォーム](https://www.dynatrace.com/support/help/technology-support/oneagent-platform-and-capability-support-matrix/#cloud-application-platforms)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud で分散トレースを使用する](how-to-distributed-tracing.md)
