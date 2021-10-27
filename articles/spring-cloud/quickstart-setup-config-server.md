---
title: クイックスタート - Azure Spring Cloud Config Server を設定する
description: アプリのデプロイのための Azure Spring Cloud Config Server の設定について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java, fasttrack-edit
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f4b4eeaac6e32335937f42e1b35abe3929f6e972
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005263"
---
# <a name="quickstart-set-up-azure-spring-cloud-config-server"></a>クイックスタート: Azure Spring Cloud Config Server を設定する

Azure Spring Cloud Config Server は、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリ レイヤーを使用します。 このクイックスタートでは、Git リポジトリからデータを取得するように Config Server を設定します。

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>前提条件

* このシリーズの先行する次のクイックスタートを完了しておきます。「[Azure Spring Cloud サービスのプロビジョニング](./quickstart-provision-service-instance.md)」。

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud Config Server の手順

次のコマンドを実行し、プロジェクトの git リポジトリの場所を使用して、Config Server を設定します。 *\<service instance name>* を、以前に作成したサービスの名前に置き換えます。 前のクイックスタートで設定したサービス インスタンス名の既定値は、このコマンドでは機能しません。

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

このコマンドは Config Server に、サンプル アプリ リポジトリの [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) フォルダー内の構成データを検索するように指示します。 構成データを取得するアプリの名前は `planet-weather-provider` であるため、使用されるファイルは [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml) です。

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud Config Server は、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリ レイヤーを使用します。  Azure Spring Cloud にマイクロサービス アプリをデプロイするように、Config Server を設定します。

## <a name="prerequisites"></a>前提条件

* [JDK 8 または JDK 11 をインストールする](/azure/developer/java/fundamentals/java-jdk-install)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* (オプション) [Azure CLI バージョン 2.0.67 以降をインストール](/cli/azure/install-azure-cli)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする
* (オプション) [Azure Toolkit for IntelliJ をインストール](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)し、[サインイン](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)する

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud Config Server の手順

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

次の手順では、Azure portal を使用して、[PetClinic サンプル](https://github.com/azure-samples/spring-petclinic-microservices)をデプロイするように Config Server を設定します。

1. サービスの **[概要]** ページに移動し、 **[Config Server]\(構成サーバー\)** を選択します。

2. **[既定のリポジトリ]** セクションで **[URI]** を "https://github.com/azure-samples/spring-petclinic-microservices-config" に設定します。

3. **[検証]** を選択します。

    ![Config Server への移動](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. 検証が完了したら、 **[適用]** を選択して変更を保存します。

    ![Config Server の検証](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. 構成の更新に数分かかる場合があります。

    ![Config Server の更新](media/spring-cloud-quickstart-launch-app-portal/updating-config.png)

6. 構成が完了すると、通知が届きます。

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

次の手順では、Azure CLI を使用して、[Pet Clinic サンプル](https://github.com/azure-samples/spring-petclinic-microservices)をデプロイするように Config Server を設定します。

次のコマンドを実行して、既定のリポジトリを設定します。

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/azure-samples/spring-petclinic-microservices-config
```

::: zone-end

> [!TIP]
> Config Server にプライベート リポジトリを使用している場合は、[認証の設定に関するチュートリアル](./how-to-config-server.md)を参照してください。

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Azure Spring Cloud Config Server のトラブルシューティング

次の手順では、Config Server の設定のトラブルシューティングを行う方法について説明します。

1. Azure portal でサービスの **[概要]** ページに移動し、 **[ログ]** を選択します。
1. **[クエリ]** を選択し、 **[Show the application logs that contain the "error" or "exception" terms"]\("エラー" または "例外" という語句を含むアプリケーション ログを表示する\)** を選択します。
1. **[実行]** を選択します。
1. ログで **java.lang.illegalStateException** エラーが見つかった場合、これは Spring Cloud サービスで Config Server からプロパティを見つけることができないことを示します。

    [ ![ASC ポータルでのクエリの実行](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. サービスの **[概要]** ページに移動します。
1. **[Diagnose and solve prolems]\(問題の診断と解決\)** を選択します。
1. **[Config Server]\(Config Server\)** 検出機能を選択します。

    [ ![ASC ポータルでの問題の診断](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

1. **[Config Server Health Check]\(Config Server の正常性チェック\)** を選択します。

    [ ![ASC ポータルでの問題](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

1. **[Config Server Status]\(Config Server の状態\)** を選択して、検出機能の詳細を表示します。

    [ ![ASC ポータルでの正常性の状態](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、サブスクリプションに残っていると課金が継続される Azure リソースを作成しました。 次のクイックスタートに進まない場合は、[リソースのクリーンアップ](./quickstart-logs-metrics-tracing.md#clean-up-resources)に関する記事を参照してください。 それ以外の場合は、次のクイックスタートに進んでください。

> [!div class="nextstepaction"]
> [アプリをビルドして配置する](./quickstart-deploy-apps.md)
