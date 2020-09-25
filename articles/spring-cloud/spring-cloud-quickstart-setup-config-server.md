---
title: クイックスタート - Azure Spring Cloud 構成サーバーを設定する
description: アプリのデプロイのための Azure Spring Cloud 構成サーバーの設定について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885701"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>クイック スタート:Azure Spring Cloud の構成サーバーを設定する

Azure Spring Cloud 構成サーバーは、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリ レイヤーを使用します。 このクイックスタートでは、Git リポジトリからデータを取得するように構成サーバーを設定します。

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>前提条件

* このシリーズの先行する次のクイックスタートを完了しておきます。「[Azure Spring Cloud サービスのプロビジョニング](spring-cloud-quickstart-provision-service-instance.md)」。

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

* [JDK 8 をインストールする](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* (オプション) [Azure CLI バージョン 2.0.67 以降をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする
* (オプション) [Azure Toolkit for IntelliJ をインストール](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)し、[サインイン](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)する

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud 構成サーバーの手順

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

次の手順では、Azure portal を使用して、[Piggymetrics サンプル](spring-cloud-quickstart-sample-app-introduction.md)をデプロイするように構成サーバーを設定します。

1. サービスの **[概要]** ページに移動し、 **[Config Server]\(構成サーバー\)** を選択します。

2. **[既定のリポジトリ]** セクションで **[URI]** を "https://github.com/Azure-Samples/piggymetrics-config" に設定します。

3. **[適用]** を選択して変更を保存します。

    ![ASC ポータルのスクリーンショット](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

次の手順では、Azure CLI を使用して、[Piggymetrics サンプル](spring-cloud-quickstart-sample-app-introduction.md)をデプロイするように構成サーバーを設定します。

次のように、プロジェクトの git リポジトリの場所を使用して、構成サーバーを設定します。

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このシリーズの次のクイックスタートに進む場合は、この手順をスキップしてください。

これらのクイックスタートでは、サブスクリプションに残っていると課金が継続される Azure リソースを作成しました。 次のクイックスタートに進む予定がなく、今後これらのリソースが必要になることが予想されない場合は、ポータルを使用してリソース グループを削除するか、Cloud Shell で次のコマンドを実行します。

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

前のクイックスタートでは、既定のリソース グループ名も設定しました。 次のクイックスタートに進まない場合は、次の CLI コマンドを実行して、既定値をクリアします。

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリをビルドして配置する](spring-cloud-quickstart-deploy-apps.md)
