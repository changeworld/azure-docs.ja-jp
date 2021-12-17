---
title: Form Recognizer コンテナーの構成
titleSuffix: Azure Applied AI Services
description: Form Recognizer コンテナーを構成してフォームとテーブルのデータを解析する方法を学習します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: a7a42f9a2078a3384949704a6319c8acbedcb17d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326454"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer コンテナーの構成

> [!IMPORTANT]
>
> Form Recognizer コンテナーは、限定的なプレビュー段階にあります。 これを使用するには、[オンライン リクエスト](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)を送信し、承認を受ける必要があります。 詳細については、以下の [**コンテナーを実行するための承認を要求する**](form-recognizer-container-install-run.md#request-approval-to-run-the-container)方法に関するセクションを参照してください。

Azure Form Recognizer コンテナーを使用することで、堅牢なクラウド機能とエッジのローカル性という両方のメリットを活用できるように最適化されたアプリケーション アーキテクチャを構築できます。 コンテナーは、オンプレミスでもクラウドにも簡単に配置できる、最小限で仮想的に隔離された環境を提供します。 この記事では、`docker compose` コマンドの引数を使用して Form Recognizer コンテナーのランタイム環境を構成する方法について説明します。 Form Recognizer 機能は、**レイアウト**、**名刺**、**ID ドキュメント**、**レシート**、**請求書**、**カスタム** の 6 つの Form Recognizer 機能コンテナーでサポートされています。 これらのコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかの例については、[docker-compose.yml ファイルの例](#example-docker-composeyml-file)に関するセクションを参照してください。

## <a name="configuration-settings"></a>構成設定

各コンテナーには、次の構成設定があります。

|必須|設定|目的|
|--|--|--|
|はい|[ApiKey](#apikey-and-billing-configuration-setting)|課金情報を追跡します。|
|はい|[Billing](#apikey-and-billing-configuration-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。 取得の詳細については、_こちら_ [Billing]](form-recognizer-container-install-run.md#billing) を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../../../cognitive-services/cognitive-services-custom-subdomains.md)」を _参照_ してください。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ|[ApplicationInsights](#applicationinsights-setting)|お客様のコンテナーに対する [Azure Application Insights](/azure/application-insights) テレメトリ サポートの追加を有効にします。|
|いいえ|[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ|HTTP Proxy|送信要求を行うために、HTTP プロキシを構成します。|
|いいえ|[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |

> [!IMPORTANT]
> [`ApiKey`](#apikey-and-billing-configuration-setting)、[`Billing`](#apikey-and-billing-configuration-setting)、および [`Eula`](#eula-setting) の各設定は一緒に使用されます。 3 つの設定すべてに有効な値を指定する必要があります。そうしないと、コンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](form-recognizer-container-install-run.md#billing)」を参照してください。

## <a name="apikey-and-billing-configuration-setting"></a>ApiKey と Billing の構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値は、「Billing 構成設定」セクションで `Billing` に対して指定されたリソースの有効なキーであることが必要です。

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure のリソースのエンドポイント URI を指定します。 この構成設定の値は、Azure のリソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

 これらの設定は Azure portal の **[キーとエンドポイント]** ページで確認できます。

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal の [キーとエンドポイント] ページ。":::

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP プロキシ資格情報設定

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="volume-settings"></a>ボリュームの設定

コンテナー間でデータを読み書きするには、[**ボリューム**](https://docs.docker.com/storage/volumes/)を使用します。 ボリュームは、Docker コンテナーで生成および使用されるデータの永続化に適しています。 `volumes` オプションを指定し、`type` (バインド)、`source` (フォルダへのパス)、`target` (ファイル パス パラメーター) を指定することで、入力マウントまたは出力マウントを指定できます。

Form Recognizer コンテナーには、入力ボリュームと出力ボリュームが必要です。 入力ボリュームは読み取り専用 (`ro`) にすることができ、トレーニングおよびスコアリングに使用されるデータへのアクセスに必要です。 出力ボリュームは書き込み可能である必要があり、モデルと一時データの保管に使用します。

ホスト ボリュームの場所を示す具体的な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントのアクセス許可とホスト ボリュームの場所のアクセス許可が競合することが原因で、[ホスト コンピューター](form-recognizer-container-install-run.md#host-computer-requirements)のマウント場所にアクセスできない場合があります。

## <a name="example-docker-composeyml-file"></a>docker-compose.yml ファイルの例

**Docker の作成** 方法は、次の 3 つの手順で構成されています。

 1. Dockerfile を作成します。
 1. 隔離された環境で一緒に実行できるように、**docker-compose.yml** でサービスを定義します。
 1. `docker-compose up` を実行してサービスを起動し、実行します。

### <a name="single-container-example"></a>1 つのコンテナーの例

この例では、レイアウト コンテナー インスタンスに {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力します。

#### <a name="layout-container"></a>**レイアウト コンテナー**

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}

    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

### <a name="multiple-containers-example"></a>複数のコンテナーの例

#### <a name="receipt-and-ocr-read-containers"></a>**レシートと OCR Read のコンテナー**

この例では、レシート コンテナーに {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力し、Computer Vision 読み取りコンテナーに {COMPUTER_VISION_ENDPOINT_URI} と {COMPUTER_VISION_API_KEY} の値を入力します。

```yml
version: "3"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: cognitiveservicespreview.azurecr.io/microsoft/cognitive-services-form-recognizer-receipt:2.1
    environment:
      - EULA=accept 
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept 
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [複数のコンテナーと docker の作成コマンドの実行に関する詳細情報について確認します](form-recognizer-container-install-run.md)
