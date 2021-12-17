---
title: Form Recognizer のサンプル ラベル付けツールのデプロイ方法
titleSuffix: Azure Applied AI Services
description: 教師あり学習で役立つ、Form Recognizer のサンプル ラベル付けツールのさまざまなデプロイ方法について説明します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c5a260a1f5c1ab6cd6232dbd0ea9c1035a110cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027020"
---
# <a name="deploy-the-sample-labeling-tool"></a>サンプル ラベル付けツールのデプロイ

Form Recognizer のサンプル ラベル付けツールは、教師あり学習用にフォーム (ドキュメント) に手動でラベルを付けることができる、簡単なユーザー インターフェイス (UI) を備えたアプリケーションです。 この記事では、次の方法についてのリンクと説明を提供します。

* [サンプル ラベル付けツールをローカルで実行する](#run-the-sample-labeling-tool-locally)
* [サンプル ラベル付けツールを Azure コンテナー インスタンス (ACI) にデプロイする](#deploy-with-azure-container-instances-aci)
* [オープンソースの OCR Form Labeling Tool を使用して、それに貢献する](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>サンプル ラベル付けツールをローカルで実行する

データのラベル付けを最も簡単に始めるには、サンプル ラベル付けツールをローカルで実行します。 以下のクイックスタートでは、Form Recognizer REST API とサンプル ラベル付けツールを使用し、手動でラベル付けされたデータでカスタム モデルをトレーニングします。

* [Azure Form Recognizer の概要](label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>Azure Container Instances (ACI) を使用してデプロイする

始める前に、Azure コンテナー インスタンス (ACI) には、2 つの方法でサンプル ラベル付けツールをデプロイできることを留意することが重要です。 いずれのオプションも、ACI を使用してサンプル ラベル付けツールを実行します。

* [Azure Portal の使用](#azure-portal)
* [Azure CLI の使用](#azure-cli)

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して新しいリソースを作成するには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com/signin/index/) にサインインします。
2. **[リソースの作成]** を選択します。
3. 次に、 **[Web アプリ]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![[Web アプリ] を選択する](./media/quickstarts/create-web-app.png)

4. 最初に、 **[基本]** タブが選択されていることを確認します。 次に、いくつかの情報を指定する必要があります。

   > [!div class="mx-imgBorder"]
   > ![[基本] を選択する](./media/quickstarts/select-basics.png)
   * [サブスクリプション] - 既存の Azure サブスクリプションを選択します
   * [リソース グループ] - 既存のリソース グループを再利用するか、このプロジェクト用に新しいリソース グループを作成することができます。 新しいリソース グループを作成することをお勧めします。
   * [名前] - Web アプリの名前を指定します。
   * [発行] - **[Docker コンテナー]** を選択します
   * [オペレーティング システム] - **[Linux]** を選択します
   * [リージョン] - 適切なリージョンを選択します。
   * [Linux プラン] - App Service の価格レベルとプランを選択します。

   > [!div class="mx-imgBorder"]
   > ![Web アプリ を構成する](./media/quickstarts/select-docker.png)

5. 次に、 **[Docker]** タブを選択します。

   > [!div class="mx-imgBorder"]
   > ![[Docker] を選択する](./media/quickstarts/select-docker.png)

6. ここでは、Docker コンテナーを構成します。 特に注意がない限り、すべてのフィールドが必須です。
<!-- markdownlint-disable MD025 -->

* [オプション] - **[単一コンテナー]** を選択します
* [イメージ ソース] - **[プライベート レジストリ]** を選択します
* [サーバー URL] - `https://mcr.microsoft.com` に設定します
* [ユーザー名] (省略可能) - ユーザー名を作成します。
* [パスワード] (省略可能) - 憶えやすい安全なパスワードを作成します。
* [イメージとタグ] - `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1` に設定します
* [継続的配置] - 開発チームがサンプル ラベル付けツールを変更したときに自動更新を受信する場合は、これを **オン** に設定します。
* [スタートアップ コマンド] - `./run.sh eula=accept` に設定します

> [!div class="mx-imgBorder"]
> ![Docker を構成する](./media/quickstarts/configure-docker.png)

* 次に、 **[確認と作成]** を選択し、 **[作成]** を選択して Web アプリをデプロイします。 完了すると、リソースの **[概要]** で指定した URL で Web アプリにアクセスできるようになります。

### <a name="continuous-deployment"></a>継続的なデプロイ

Web アプリを作成したら、継続的配置オプションを有効にすることができます。

* 左側のウィンドウで、 **[コンテナーの設定]** を選択します。
* メイン ウィンドウで、[継続的配置] に移動し、 **[オン]** と **[オフ]** ボタンを切り替え、基本設定を設定します。

:::image type="content" source="media/label-tool/continuous-deployment.png" alt-text="スクリーンショット: [コンテナーの設定] の [継続的配置] の拡大表示。" lightbox="media/label-tool/continuous-deployment-bigger.png":::

> [!NOTE]
> Web アプリを作成するときに、承認と認証を構成することもできます。 これは、開始するために必要なことではありません。

> [!IMPORTANT]
> Web アプリが `https` アドレスで表示されるようにするために、TLS を有効にしなければならない場合があります。 [TLS エンドポイントを有効にする](../../container-instances/container-instances-container-group-ssl.md)ための手順に従って、Web アプリの TLS/SSL を有効にするサイドカー コンテナーを設定してください。
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

Azure portal を使用する代わりに、Azure CLI を使用してリソースを作成することもできます。 続ける前に、[Azure CLI](/cli/azure/install-azure-cli) をインストールする必要があります。 既に Azure CLI を使用している場合は、このステップを省略できます。

このコマンドについて知っておくべきことがいくつかあります。

* `DNS_NAME_LABEL=aci-demo-$RANDOM` ではランダムな DNS 名が生成されます。
* このサンプルでは、リソースの作成に使用できるリソース グループがあることを前提としています。 `<resource_group_name>` は、サブスクリプションに関連付けられている有効なリソース グループに置き換えます。
* リソースを作成する場所を指定する必要があります。 `<region name>` は、Web アプリの目的のリージョンに置き換えます。
* このコマンドでは、EULA が自動的に受け入れられます。

Azure CLI から次のコマンドを実行して、サンプル ラベル付けツール用の Web アプリ リソースを作成します。

<!-- markdownlint-disable MD024 -->

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1 \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

### <a name="connect-to-azure-ad-for-authorization"></a>承認のために Azure AD に接続する

Web アプリを Azure Active Directory (Azure AD) に接続することをお勧めします。 この接続により、有効な資格情報を持つユーザーのみがサインインして Web アプリを使用できるようになります。 [App Service アプリの構成](../../app-service/configure-authentication-provider-aad.md)に関する記事の手順に従って、Azure Active Directory に接続します。

## <a name="open-source-on-github"></a>GitHub でのオープンソース

GitHub のオープンソース プロジェクトの OCR Form Labeling Tool を使用することもできます。 このツールは、React + Redux を使用してビルドされ、TypeScript で記述された Web アプリケーションです。 詳細については、または投稿に関しては、[OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[ラベルを使用したトレーニング](label-tool.md)のクイックスタートを使用し、ツールを使用してトレーニング データに手動でラベルを付ける方法、および教師あり学習を行う方法について学習します。
