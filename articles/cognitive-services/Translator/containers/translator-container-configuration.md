---
title: コンテナーを構成する - Translator
titleSuffix: Azure Cognitive Services
description: Translator コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 必須とオプションの両方の設定があります。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f298a4593ef5aa8af9b7f62f48b4e32075429dbf
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539594"
---
# <a name="configure-translator-docker-containers-preview"></a>Translator Docker コンテナーを構成する (プレビュー)

Cognitive Services では、各コンテナーに共通の構成フレームワークが用意されています。  Translator コンテナーを簡単に構成し、堅牢なクラウド機能とエッジの局所性のために最適化された Translator アプリケーション アーキテクチャを構築することができます。

**Translator** コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 このコンテナーに固有の設定は、課金設定です。

## <a name="configuration-settings"></a>構成設定

このコンテナーには、次の構成設定があります。

|必須|設定|目的|
|--|--|--|
|はい|[ApiKey](#apikey-configuration-setting)|課金情報を追跡します。|
|いいえ|[ApplicationInsights](#applicationinsights-setting)|お客様のコンテナーに対する [Azure Application Insights](/azure/application-insights) テレメトリ サポートの追加を有効にします。|
|はい|[Billing](#billing-configuration-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ|[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ|HTTP Proxy|送信要求を行うために、HTTP プロキシを構成します。|
|いいえ|[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|Yes|[Mounts](#mount-settings)|ホスト コンピューターからコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|

 > [!IMPORTANT]
> [**ApiKey**](#apikey-configuration-setting)、[**Billing**](#billing-configuration-setting)、[**EULA**](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、ご確認ください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Translator_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal: **Translator** の [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の _Translator_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Translator_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal: **Translator** の [概要] ページのラベル `Endpoint`

| 必須 | 名前 | データ型 | 説明 |
| -------- | ---- | --------- | ----------- |
| はい | `Billing` | String | 課金エンドポイント URI。 課金 URI の取得の詳細については、「[必須パラメーターの収集](translator-how-to-install-container.md#required-elements)」を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../../cognitive-services-custom-subdomains.md)」を参照してください。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP プロキシ資格情報設定

[!INCLUDE [Container shared HTTP proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cognitive Services コンテナーの詳細情報](../../cognitive-services-container-support.md)
