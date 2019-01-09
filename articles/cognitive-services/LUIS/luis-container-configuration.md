---
title: Docker コンテナーの設定
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 LUIS には、いくつかの必須の設定と省略可能な設定があります。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080267"
---
# <a name="configure-containers"></a>コンテナーを構成する

Language Understanding (LUIS) コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 LUIS には、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、入力[マウント設定](#mount-settings)と課金設定です。 

コンテナーの設定は[階層](#hierarchical-settings)になっていて、[環境変数](#environment-variable-settings)または Docker の[コマンドライン引数](#command-line-argument-settings)を使用して設定できます。

## <a name="configuration-settings"></a>構成設定

このコンテナーには、次の構成設定があります。

|必須|Setting|目的|
|--|--|--|
|[はい]|[ApiKey](#apikey-setting)|課金情報の追跡に使用されます。|
|いいえ |[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリ サポートをお客様のコンテナーに追加できます。|
|[はい]|[課金](#billing-setting)|Azure 上の _Language Understanding_ リソースのエンドポイント URI を指定します。|
|[はい]|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ |[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ |[ログ](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|[はい]|[Mounts](#mount-settings)|[ホスト コンピューター](luis-container-howto.md#the-host-computer)からコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)、[`Billing`](#billing-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](luis-container-howto.md#billing)」を参照してください。

## <a name="apikey-setting"></a>ApiKey 設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-setting) 構成設定に指定された _Language Understanding_ リソースの有効なキーであることが必要です。

この設定は次の 2 か所で確認できます。

* Azure portal: **Language Understanding** の [リソース管理] の **[キー]**
* LUIS ポータル: **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページ。 

スターター キーまたはオーサリング キーは使用しないでください。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

`ApplicationInsights` 設定を使用すると、[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリのサポートをお客様のコンテナーに追加できます。 Application Insights によってお客様のコンテナーを詳細に監視できます。 コンテナーの可用性、パフォーマンス、利用状況を簡単に監視できます。 さらに、お客様のコンテナーのエラーを迅速に特定して診断することもできます。

次の表に、`ApplicationInsights` セクションでサポートされている構成設定について説明します。

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|いいえ | `InstrumentationKey` | String | コンテナーのテレメトリ データの送信先の Application Insights インスタンスのインストルメンテーション キー。 詳細については、「[Application Insights for ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)」を参照してください。 <br><br>例:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Billing 設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の _Language Understanding_ リソースのエンドポイント URI を指定します。 この構成設定の値は指定する必要があり、値は Azure の _Language Understanding_ リソースの有効なエンドポイント URI である必要があります。

この設定は次の 2 か所で確認できます。

* Azure portal: **Language Understanding** の [概要]。`Endpoint` として表示されます。
* LUIS ポータル: **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページ。エンドポイント URI の一部として表示されます。

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|[はい]| `Billing` | String | 課金エンドポイント URI<br><br>例:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 設定

`Eula` 設定は、コンテナーのライセンスに同意済みであることを示します。 この構成設定の値を指定する必要があり、値を `accept` に設定する必要があります。

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|[はい]| `Eula` | String | ライセンスへの同意<br><br>例:<br>`Eula=accept` |

Cognitive Services のコンテナーは、Azure の使用について定める[契約](https://go.microsoft.com/fwlink/?linkid=2018657)の下でライセンスされます。 Azure の使用について定める契約をまだ結んでいない場合、Azure の使用について定める契約が[マイクロソフト オンライン サブスクリプション契約](https://go.microsoft.com/fwlink/?linkid=2018755) ([オンライン サービス規約](https://go.microsoft.com/fwlink/?linkid=2018760)を含む) であることに同意するものとします。 また、プレビューに関しては、「[Microsoft Azure プレビューの追加使用条件](https://go.microsoft.com/fwlink/?linkid=2018815)」にも同意するものとします。 コンテナーの使用をもって、お客様はこれらの規約に同意したものとします。

## <a name="fluentd-settings"></a>Fluentd の設定

Fluentd は、ログの一元管理を実現するオープンソースのデータ コレクターです。 [Fluentd](https://www.fluentd.org) サーバーに対するコンテナーの接続は、`Fluentd` の設定によって管理されます。 LUIS コンテナーには、お客様のコンテナーでログ、および必要に応じてメトリック データを Fluentd サーバーに書き込むことができる Fluentd ログ プロバイダーが含まれます。

次の表に、`Fluentd` セクションでサポートされている構成設定について説明します。

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `Host` | String | Fluentd サーバーの IP アドレスまたは DNS ホスト名。 |
| `Port` | 整数 | Fluentd サーバーのポート。<br/> 既定値は 24224 です。 |
| `HeartbeatMs` | 整数 | ハートビート間隔 (ミリ秒)。 この間隔が期限切れになるまでにイベント トラフィックが送信されなかった場合、ハートビートが Fluentd サーバーに送信されます。 既定値は、60000 ミリ秒 (1 分) です。 |
| `SendBufferSize` | 整数 | 送信操作用に割り当てられたネットワーク バッファー領域 (バイト数)。 既定値は、32,768 バイト (32 キロバイト) です。 |
| `TlsConnectionEstablishmentTimeoutMs` | 整数 | Fluentd サーバーとの SSL または TLS 接続を確立するためのタイムアウト (ミリ秒)。 既定値は、10000 ミリ秒 (10 秒) です。<br/> `UseTLS` が false に設定されている場合、この値は無視されます。 |
| `UseTLS` | Boolean | コンテナーで、Fluentd サーバーとの通信に SSL または TLS を使用する必要があるかどうかを示します。 既定値は false です。 |

## <a name="logging-settings"></a>Logging の設定

`Logging` の設定では、お客様のコンテナーの ASP.NET Core ログ サポートを管理します。 お客様が ASP.NET Core アプリケーションに対して使用するのと同じ構成設定と値をお客様のコンテナーに使用できます。 

LUIS コンテナーでは、次のログ プロバイダーがサポートされています。

|プロバイダー|目的|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。|
|[デバッグ](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。|
|[ディスク](#disk-logging)|JSON ログ プロバイダー。 このログ プロバイダーは、ログ データを出力マウントに書き込みます。|

### <a name="disk-logging"></a>Disk ログ
  
`Disk` ログ プロバイダーでは、次の構成設定がサポートされます。  

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `Format` | String | ログ ファイルの出力形式。<br/> **注:** ログ プロバイダーを有効にするためにこの値を `json` に設定する必要があります。 コンテナーのインスタンス化中に、出力マウントを指定せずに、この値を指定した場合、エラーが発生します。 |
| `MaxFileSize` | 整数 | ログ ファイルの最大サイズ (メガバイト (MB))。 現在のログ ファイルのサイズがこの値を満たしているか、または超えている場合、ログ プロバイダーによって新しいログ ファイルが開始されます。 -1 が指定されている場合、ログ ファイルのサイズは、出力マウントの最大ファイル サイズ (存在する場合) によってのみ制限されます。 既定値は 1 です。 |

ASP.NET Core ログのサポートを構成する方法の詳細については、[設定ファイル構成](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)に関するページを参照してください。

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。 

LUIS コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](luis-container-howto.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

次の表で、サポートされる設定について説明します。

|必須| Name | データ型 | 説明 |
|-------|------|-----------|-------------|
|[はい]| `Input` | String | 入力マウントのターゲット。 既定値は `/input` です。 これは LUIS パッケージ ファイルの保存先です。 <br><br>例:<br>`--mount type=bind,src=c:\input,target=/input`|
|いいえ | `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 LUIS のクエリ ログやコンテナー ログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>階層的な設定

LUIS コンテナーの設定は階層になっていて、共有された階層が[ホスト コンピューター](luis-container-howto.md#the-host-computer)上のすべてのコンテナーで使用されます。

次のいずれかを使用して設定を指定できます。

* [環境変数](#environment-variable-settings)
* [コマンドライン引数](#command-line-argument-settings)

環境変数の値によってコマンドライン引数の値がオーバーライドされ、さらにコンテナー イメージの既定値がオーバーライドされます。 同じ構成設定に、環境変数とコマンドライン引数で異なる値を指定した場合、環境変数の値が、インスタンス化されたコンテナーによって使用されます。

|優先順位|設定の場所|
|--|--|
|1|環境変数| 
|2|コマンド ライン|
|3|コンテナー イメージの既定値|

### <a name="environment-variable-settings"></a>環境変数の設定

環境変数を使用する利点は次のとおりです。

* 複数の設定を構成できる。
* 複数のコンテナーで同じ設定を使用できる。

### <a name="command-line-argument-settings"></a>コマンドライン引数の設定

コマンドライン引数を使用する利点は、各コンテナーで異なる設定を使用できることです。

## <a name="example-docker-run-commands"></a>docker run コマンドの例

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](luis-container-howto.md#stop-the-container)するまで動作し続けます。


* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{_<引数名>_} はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
|{ENDPOINT_KEY} | トレーニング済み LUIS アプリケーションのエンドポイント キー。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | 課金エンドポイントの値は、Azure portal の [Language Understanding Overview]\(Language Understanding の概要\) ページで確認できます。|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](luis-container-howto.md#billing)」を参照してください。
> ApiKey の値は、LUIS ポータルの [Keys and Endpoints]\(キーとエンドポイント\) ページにある **[キー]** です。また、[Azure Language Understanding Resource keys]\(Azure Language Understanding リソース キー\) ページで確認することもできます。 

### <a name="basic-example"></a>基本的な例

次の例には、コンテナーを実行できるだけの最低限の引数が含まれています。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> 上記のコマンドでは、Windows 上のアクセス許可の競合を防ぐために、`c:` ドライブのディレクトリを使用しています。 特定のディレクトリを入力ディレクトリとして使用する必要がある場合は、Docker サービスのアクセス許可の付与が必要なことがあります。 上記の Docker コマンドでは、行連結文字としてバック スラッシュ (`\`) が使用されています。 使用している[ホスト コンピューター](luis-container-howto.md#the-host-computer)のオペレーティング システムの要件に応じて、置換または削除してください。 Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。


### <a name="applicationinsights-example"></a>ApplicationInsights の例

次の例では、コンテナーの実行中にテレメトリを Application Insights に送信するように、ApplicationInsights の引数を設定しています。

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>コマンドライン引数を使用したログの例

次のコマンドでは、ログ レベル (`Logging:Console:LogLevel`) を設定することにより、ログ レベルを [`Information`](https://msdn.microsoft.com) に構成しています。 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>環境変数を使用したログの例

次のコマンドでは、`Logging:Console:LogLevel` という名前の環境変数を使用して、ログ レベルを [`Information`](https://msdn.microsoft.com) に構成しています。 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>次の手順

* [コンテナーのインストール方法と実行方法](luis-container-howto.md)を確認する。
* [よくあるご質問 (FAQ)](luis-resources-faq.md) を参照して、LUIS 機能に関連する問題を解決する。