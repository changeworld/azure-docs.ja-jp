---
title: コンテナーを構成する
titlesuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 7e993b9ccc57359ac64186765b7b704535eb5a57
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086676"
---
# <a name="configure-containers"></a>コンテナーを構成する

Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。

## <a name="configuration-settings"></a>構成設定

Text Analytics コンテナーの構成設定は階層構造であり、すべてのコンテナーで、次の最上位レベル構造に基づいて、共有の階層が使われます。

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [認証](#authentication-configuration-settings)
* [課金](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [ログ](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

Text Analytics コンテナーからコンテナーをインスタンス化する場合、[環境変数](#configuration-settings-as-environment-variables)または[コマンドライン引数](#configuration-settings-as-command-line-arguments)を使用して、構成設定を指定できます。

環境変数の値によってコマンドライン引数の値がオーバーライドされ、さらにコンテナー イメージの既定値がオーバーライドされます。 つまり、`Logging:Disk:LogLevel` などの同じ構成設定に、環境変数とコマンドライン引数で異なる値を指定した場合、インスタンス化されたコンテナーによって、環境変数の値が使われます。

### <a name="configuration-settings-as-environment-variables"></a>環境変数としての構成設定

[ASP.NET Core 環境変数構文](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment)を使用して、構成設定を指定できます。

コンテナーは、インスタンス化されるときに、ユーザー環境変数を読み取ります。 環境変数が存在する場合、環境変数の値によって、指定された構成設定の既定値がオーバーライドされます。 環境変数を使用する利点は、コンテナーをインスタンス化する前に複数の構成設定を設定でき、複数のコンテナーで、同じ一連の構成設定を自動的に使用できることです。

たとえば、次のコマンドでは環境変数を使用して、コンソール ログ レベルを [LogLevel.Information](https://msdn.microsoft.com) に構成し、次に Sentiment Analysis コンテナー イメージからコンテナーをインスタンス化しています。 環境変数の値によって、既定の構成設定がオーバーライドされます。

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>コマンドライン引数としての構成設定

[ASP.NET Core コマンドライン引数構文](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments)を使用して、構成設定を指定できます。

構成設定は、ダウンロードしたコンテナー イメージからコンテナーをインスタンス化するために使用する [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドのオプションの `ARGS` パラメーターに指定できます。 コマンドライン引数を使用する利点は、各コンテナーで、構成設定のさまざまなカスタム セットを使用できることです。

たとえば、次のコマンドでは、Sentiment Analysis コンテナー イメージからコンテナーをインスタンス化し、コンソール ログ レベルを LogLevel.Information に構成して、既定の構成設定をオーバーライドしています。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 構成設定は、コンテナーの課金情報を追跡するために使用される Azure の Text Analytics リソースの構成キーを指定します。 この構成設定の値を指定する必要があり、その値は [`Billing`](#billing-configuration-setting) 構成設定に指定された Text Analytics リソースの有効な構成キーである必要があります。

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-configuration-setting) 構成設定は一緒に使用し、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、コンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。

## <a name="applicationinsights-configuration-settings"></a>ApplicationInsights 構成設定

`ApplicationInsights` セクションの構成設定により、[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリ サポートをコンテナーに追加できます。 Application Insights では、コンテナーをコード レベルまで詳細に監視できます。 コンテナーの可用性、パフォーマンス、利用状況を簡単に監視できます。 さらに、コンテナーのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。

次の表に、`ApplicationInsights` セクションでサポートされている構成設定について説明します。

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `InstrumentationKey` | String | コンテナーのテレメトリ データの送信先の Application Insights インスタンスのインストルメンテーション キー。 詳細については、「[Application Insights for ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)」を参照してください。 |

## <a name="authentication-configuration-settings"></a>Authentication 構成設定

`Authentication` 構成設定は、コンテナーの Azure セキュリティ オプションを提供します。 このセクションの構成設定は Text Analytics コンテナー内のすべてのコンテナーで使用できますが、構成設定の値を使用する方法は各コンテナーに固有であり、コンテナーがこのセクションをまったく使用しない場合もあります。

次の表に、`Authentication` セクションでサポートされている構成設定について説明します。

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `ApiKey` | 文字列、配列 | コンテナーで他の Azure リソースにアクセスする必要がある場合に、コンテナーによって使用される Azure サブスクリプション キー。<br/> コンテナーで複数のサブスクリプション キーが使用される場合は、この値を文字列の配列として指定します。それ以外の場合は、文字列値を使用して、コンテナーによって使用される 1 つのサブスクリプション キーを指定します。 |

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 構成設定は、コンテナーの課金情報を測定するために使用される Azure の Text Analytics リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の Text Analytics リソースの有効なエンドポイント URI である必要があります。

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-configuration-setting) 構成設定は一緒に使用し、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、コンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。

## <a name="eula-configuration-setting"></a>Eula 構成設定

`Eula` 構成設定は、コンテナーのライセンスに同意したことを示します。 この構成設定の値を指定する必要があり、値を `accept` に設定する必要があります。

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-configuration-setting) 構成設定は一緒に使用し、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、コンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。

Cognitive Services のコンテナーは、Azure の使用について定める[契約](https://go.microsoft.com/fwlink/?linkid=2018657)の下でライセンスされます。 Azure の使用について定める契約をまだ結んでいない場合、Azure の使用について定める契約が[マイクロソフト オンライン サブスクリプション契約](https://go.microsoft.com/fwlink/?linkid=2018755) ([オンライン サービス規約](https://go.microsoft.com/fwlink/?linkid=2018760)を含む) であることに同意するものとします。 また、プレビューに関しては、「[Microsoft Azure プレビューの追加使用条件](https://go.microsoft.com/fwlink/?linkid=2018815)」にも同意するものとします。 コンテナーの使用をもって、お客様はこれらの規約に同意したものとします。

## <a name="fluentd-configuration-settings"></a>Fluentd 構成設定

`Fluentd` セクションは、統合ログのためのオープン ソースのデータ コレクター [Fluentd](https://www.fluentd.org) の構成設定を管理します。 Text Analytics コンテナーには、コンテナーがログ、および必要に応じてメトリック データを Fluentd サーバーに書き込むことができる Fluentd ログ プロバイダーが含まれます。

次の表に、`Fluentd` セクションでサポートされている構成設定について説明します。

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `Host` | String | Fluentd サーバーの IP アドレスまたは DNS ホスト名。 |
| `Port` | 整数 | Fluentd サーバーのポート。<br/> 既定値は 24224 です。 |
| `HeartbeatMs` | 整数 | ハートビート間隔 (ミリ秒)。 この間隔が期限切れになるまでにイベント トラフィックが送信されなかった場合、ハートビートが Fluentd サーバーに送信されます。 既定値は、60000 ミリ秒 (1 分) です。 |
| `SendBufferSize` | 整数 | 送信操作用に割り当てられたネットワーク バッファー領域 (バイト数)。 既定値は、32,768 バイト (32 キロバイト) です。 |
| `TlsConnectionEstablishmentTimeoutMs` | 整数 | Fluentd サーバーとの SSL または TLS 接続を確立するためのタイムアウト (ミリ秒)。 既定値は、10000 ミリ秒 (10 秒) です。<br/> `UseTLS` が false に設定されている場合、この値は無視されます。 |
| `UseTLS` | Boolean | コンテナーで、Fluentd サーバーとの通信に SSL または TLS を使用する必要があるかどうかを示します。 既定値は false です。 |

## <a name="logging-configuration-settings"></a>Logging 構成設定

`Logging` 構成設定は、コンテナーの ASP.NET Core ログ サポートを管理します。 ASP.NET Core アプリケーションに対して使用できる同じ構成設定と値をコンテナーに使用できます。 次のログ プロバイダーが Text Analytics コンテナーでサポートされています。

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  ASP.NET Core `Console` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。
* [デバッグ](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  ASP.NET Core `Debug` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。
* ディスク  
  JSON ログ プロバイダー。 このログ プロバイダーは、ログ データを出力マウントに書き込みます。  
  `Disk` ログ プロバイダーでは、次の構成設定がサポートされます。  

  | Name | データ型 | 説明 |
  |------|-----------|-------------|
  | `Format` | String | ログ ファイルの出力形式。<br/> **注:** ログ プロバイダーを有効にするためにこの値を `json` に設定する必要があります。 コンテナーのインスタンス化中に、出力マウントを指定せずに、この値を指定した場合、エラーが発生します。 |
  | `MaxFileSize` | 整数 | ログ ファイルの最大サイズ (メガバイト (MB))。 現在のログ ファイルのサイズがこの値を満たしているか、または超えている場合、ログ プロバイダーによって新しいログ ファイルが開始されます。 -1 が指定されている場合、ログ ファイルのサイズは、出力マウントの最大ファイル サイズ (存在する場合) によってのみ制限されます。 既定値は 1 です。 |

ASP.NET Core ログのサポートを構成する方法の詳細については、[設定ファイル構成](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)に関するページを参照してください。

## <a name="mounts-configuration-settings"></a>Mounts 構成設定

Text Analytics コンテナーによって提供される Docker コンテナーは、ステートレスと不変のどちらにもなるように設計されています。 つまり、コンテナー内に作成されたファイルは、書き込み可能なコンテナー レイヤーに格納され、コンテナーの実行中にのみ維持され、簡単にアクセスできません。 そのコンテナーが停止するか、または削除された場合、そのコンテナー内に共に作成されたファイルは破棄されます。

ただし、それらは Docker コンテナーであるため、ボリュームやバインド マウントなどの Docker ストレージ オプションを使用して、コンテナーでサポートされていれば、コンテナーの外部から永続化されたデータを読み書きできます。 Docker ストレージ オプションを指定して、管理する方法の詳細については、「[Manage data in Docker](https://docs.docker.com/storage/)」(Docker でのデータの管理) を参照してください。

> [!NOTE]
> 通常、これらの構成設定の値を変更する必要はありません。 代わりに、コンテナーの入力マウントと出力マウントを指定する際に、宛先としてこれらの構成設定で指定された値を使用します。 入力マウントと出力マウントを指定する詳細については、「[入力マウントと出力マウント](#input-and-output-mounts)」を参照してください。

次の表に、`Mounts` セクションでサポートされている構成設定について説明します。

| Name | データ型 | 説明 |
|------|-----------|-------------|
| `Input` | String | 入力マウントのターゲット。 既定値は `/input` です。 |
| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 |

### <a name="input-and-output-mounts"></a>入力マウントと出力マウント

既定で各コンテナーでは、コンテナーがデータを読み取ることができる*入力マウント*とコンテナーがデータを書き込むことができる*出力マウント*をサポートできます。 入力マウントまたは出力マウントをサポートするために、コンテナーは必要ありませんが、各コンテナーでは、Text Analytics コンテナーによってサポートされるログ オプションに加えて、コンテナー固有の目的で入力マウントと出力マウントの両方を使用することができます。 次の表では、Text Analytics コンテナー内の各コンテナーに対する入力マウントと出力マウントのサポートの一覧を示します。

| コンテナー | 入力マウント | 出力マウント |
|-----------|-------------|--------------|
|[キー フレーズ抽出](#working-with-key-phrase-extraction) | サポートされていません | 省略可能 |
|[言語検出](#working-with-language-detection) | サポートされていません | 省略可能 |
|[感情分析](#working-with-sentiment-analysis) | サポートされていません | 省略可能 |

入力マウントまたは出力マウントを指定するには、ダウンロードしたコンテナー イメージからコンテナーをインスタンス化するために使用する [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定します。 既定で、入力マウントではその宛先として、`/input` が使われ、出力マウントではその宛先として `/output` が使われます。 Docker コンテナー ホストで使用できるすべての Docker ストレージ オプションは、`--mount` オプションで指定できます。

たとえば、次のコマンドでは Docker バインド マウントを、出力マウントとしてホスト コンピューター上の `D:\Output` フォルダーに定義し、さらに Sentiment Analysis コンテナー イメージからコンテナーをインスタンス化し、ログ ファイルを JSON 形式で出力マウントに保存しています。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
