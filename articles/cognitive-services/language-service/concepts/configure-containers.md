---
title: コンテナーの構成 - 言語サービス
titleSuffix: Azure Cognitive Services
description: 言語サービスでは、コンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して管理できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: ignite-fall-2021
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.openlocfilehash: 43beab44e3e56a43f9efd57bd02af2ae8d487c0f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017553"
---
# <a name="configure-language-service-docker-containers"></a>言語サービスの Docker コンテナーを構成する

言語サービスでは、コンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して管理できます。 この記事は、次のコンテナーを対象にしています。

* 感情分析
* 言語検出
* ような作業を
* Text Analytics for Health 

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 そのキーの値を指定する必要があります。また、それは、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Language_ リソースの有効なキーであることが必要です。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の _Language_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Language_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI。 |


## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http プロキシ資格情報設定

[!INCLUDE [Container shared configuration proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定
 
[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。

言語サービス コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使われることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使われるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、ホスト コンピューターのマウント場所にアクセスできないこともあります。 

|省略可能| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | String | 言語サービス コンテナーでは、これは使用されません。|
|省略可能| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>次のステップ

* さらに [Cognitive Services コンテナー](../../cognitive-services-container-support.md)を使用する
