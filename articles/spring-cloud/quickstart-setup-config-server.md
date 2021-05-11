---
title: クイックスタート - Azure Spring Cloud 構成サーバーを設定する
description: アプリのデプロイのための Azure Spring Cloud 構成サーバーの設定について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, fasttrack-edit
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8c71e76213011beaa63deeaadfa3d6d2dc0d4ce2
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108286540"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>クイック スタート:Azure Spring Cloud の構成サーバーを設定する

Azure Spring Cloud 構成サーバーは、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリ レイヤーを使用します。 このクイックスタートでは、Git リポジトリからデータを取得するように構成サーバーを設定します。

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>前提条件

* このシリーズの先行する次のクイックスタートを完了しておきます。「[Azure Spring Cloud サービスのプロビジョニング](./quickstart-provision-service-instance.md)」。

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud 構成サーバーの手順

次のコマンドを実行し、プロジェクトの git リポジトリの場所を使用して、構成サーバーを設定します。 `<service instance name>` を、以前に作成したサービスの名前に置き換えます。 前のクイックスタートで設定したサービス インスタンス名の既定値は、このコマンドでは機能しません。

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

このコマンドは構成サーバーに、サンプル アプリ リポジトリの [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) フォルダー内の構成データを検索するように指示します。 構成データを取得するアプリの名前は `planet-weather-provider` であるため、使用されるファイルは [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml) です。

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud 構成サーバーは、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリ レイヤーを使用します。  Azure Spring Cloud にマイクロサービス アプリをデプロイするように、構成サーバーを設定します。

## <a name="prerequisites"></a>前提条件

* [JDK 8 をインストールする](/java/azure/jdk/)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* (オプション) [Azure CLI バージョン 2.0.67 以降をインストール](/cli/azure/install-azure-cli)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする
* (オプション) [Azure Toolkit for IntelliJ をインストール](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)し、[サインイン](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)する

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud 構成サーバーの手順

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

次の手順では、Azure portal を使用して、[PetClinic サンプル](https://github.com/azure-samples/spring-petclinic-microservices)をデプロイするように構成サーバーを設定します。

1. サービスの **[概要]** ページに移動し、 **[Config Server]\(構成サーバー\)** を選択します。

2. **[既定のリポジトリ]** セクションで **[URI]** を "https://github.com/azure-samples/spring-petclinic-microservices-config" に設定します。

3. **[検証]** をクリックします。

    ![構成サーバーへの移動](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. 検証が完了したら、 **[適用]** をクリックして変更内容を保存します。

    ![構成サーバーの検証](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. 構成の更新に数分かかる場合があります。
 
    ![構成サーバーの更新](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. 構成が完了すると、通知が届きます。

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)


次の手順では、Azure CLI を使用して、[Pet Clinic サンプル](https://github.com/azure-samples/spring-petclinic-microservices)をデプロイするように構成サーバーを設定します。

次のコマンドを実行して、既定のリポジトリを設定します。

```azurecli

az spring-cloud config-server git set -n <service instance name> --uri https://github.com/azure-samples/spring-petclinic-microservices-config
::: zone-end

> [!TIP]
> If you are using a private repository for config server, please refer to our [tutorial on setting up authentication](./how-to-config-server.md).

## Troubleshooting of Azure Spring Cloud config server

The following procedure explains how to troubleshoot config server settings.

1. In the Azure portal, go to the service **Overview** page and select **Logs**. 
1. Select **Queries** and **Show the application logs that contain the "error" or "exception" terms"**. 
1. Click **Run**. 
1. If you find the error **java.lang.illegalStateException** in logs, this indicates that spring cloud service cannot locate properties from config server.

    [ ![ASC portal run query](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Go to the service **Overview** page.
1. Select **Diagnose and solve problems**. 
1. Select **Config Server** detector.

    [ ![ASC portal diagnose problems](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Click **Config Server Health Check**.

    [ ![ASC portal genie](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Click **Config Server Status** to see more details from the detector.

    [ ![ASC portal health status](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## Next steps

In this quickstart, you created Azure resources that will continue to accrue charges if they remain in your subscription. If you don't intend to continue on to the next quickstart, see [Clean up resources](./quickstart-logs-metrics-tracing.md#clean-up-resources). Otherwise, advance to the next quickstart:

> [!div class="nextstepaction"]
> [Build and deploy apps](./quickstart-deploy-apps.md)
