<properties
 pageTitle="IoT Hub の開発者ガイド トピック | Microsoft Azure"
 description="IoT Hub のエンドポイント、セキュリティ、デバイス ID レジストリ、メッセージングなどについて説明する、Azure IoT Hub の開発者ガイドです。"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/02/2016" 
 ms.author="dobett"/>

# Azure IoT Hub 開発者ガイド

Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、完全に管理されたサービスです。

Azure IoT Hub は、以下のものを実現するのに役立ちます。

* デバイスごとのセキュリティ資格情報とアクセス制御を使用した、通信のセキュリティ保護。
* 非常にスケール性が高く信頼性に優れた、デバイスとクラウド間の双方向メッセージング。
* 最も一般的な言語とプラットフォームのデバイスのライブラリを使用した、簡単なデバイスの接続。

この記事では、次のトピックについて説明します。

- [エンドポイント](#endpoints)。このセクションでは、各 IoT Hub がランタイムと管理の操作のために公開する、さまざまなエンドポイントについて説明します。
- [デバイス ID レジストリ](#device-identity-registry)。このセクションでは、各 IoT ハブのデバイス ID レジストリに格納されている情報と、レジストリにアクセスして変更する方法について説明します。
- [セキュリティ](#security)。このセクションでは、IoT Hub の機能へのアクセスを許可するために使用する、デバイスとクラウド コンポーネントの両方のセキュリティ モデルについて説明します。
- [メッセージング](#messaging)。このセクションでは、IoT Hub が公開している (デバイスとクラウド間の双方向の) メッセージング機能について説明します。
- [クォータとスロットル](#throttling)。このセクションでは、IoT Hub の使用に適用されるクォータの概要ついて説明します。

## エンドポイント<a id="endpoints"></a>

Azure IoT Hub はさまざまなアクターに機能を公開するマルチテナント サービスです。次の図は、IoT Hub が公開しているさまざまなエンドポイントを示します。

![IoT Hub エンドポイント][img-endpoints]

エンドポイントの説明を次に示します。

* **リソース プロバイダー**。IoT Hub のリソース プロバイダーは、[Azure Resource Manager][lnk-arm] のインターフェイスを公開します。このインターフェイスにより、Azure サブスクリプションの所有者は、IoT Hub を作成、削除したり、IoT Hub のプロパティを更新したりできます。IoT Hub のプロパティでは、デバイスレベルのアクセス制御 (この記事の後ろの方にある「[アクセス制御](#accesscontrol)」セクションを参照) ではなく、ハブレベルのセキュリティ ポリシーと、C2D (クラウドからデバイス) と D2C (デバイスからクラウド) のメッセージング機能のオプションを制御します。またリソース プロバイダーにより、[デバイス ID をエクスポート](#importexport)することもできます。
* **デバイス ID の管理**。各 IoT Hub は、デバイス ID の管理 (作成、取得、更新、削除) を行うための、一連の HTTP REST エンドポイントを公開します。デバイス ID は、デバイスの認証とアクセス制御に使用されます。詳細については、「[デバイス ID レジストリ](#device-identity-registry)」を参照してください。
* **デバイスのエンドポイント**。IoT Hub は、デバイス ID レジストリでプロビジョニングされたデバイスごとに、デバイスがメッセージの送受信に使用できる一連のエンドポイントを公開します。
    - *D2C メッセージの送信*。このエンドポイントを使用して、D2C メッセージを送信します。詳細については、「[D2C メッセージング](#d2c)」を参照してください。
    - *C2D メッセージの受信*。デバイスは、このエンドポイントを使用して、そのデバイスが宛先となっている C2D メッセージを受信します。詳細については、「[C2D メッセージング](#c2d)」を参照してください。
    - *ファイルのアップロードの開始*。デバイスでは、ファイルをアップロードするために、このエンドポイントを使用して、IoT Hub から Azure Storage の SAS URI を受け取ります。詳細については、「[ファイルのアップロード](#fileupload)」を参照してください。

    これらのエンドポイントは、[MQTT v3.1.1][lnk-mqtt]、HTTP 1.1、および [AMQP 1.0][lnk-amqp] の各プロトコルを使用して公開されます。なお、AMQP は、ポート 443 で [WebSocket][lnk-websockets] 経由で使用することもできます。
* **サービス エンドポイント**。各 IoT Hub は、アプリケーション バックエンドがデバイスとの通信に使用できる一連のエンドポイントを公開します。これらのエンドポイントは、現在 [AMQP][lnk-amqp] プロトコルのみを使用して公開されています。
    - *D2C メッセージの受信*。このエンドポイントには、[Azure Event Hubs][lnk-event-hubs] との互換性があります。バックエンド サービスはこのエンドポイントを使用して、デバイスから送信されたあらゆる D2C メッセージを読み取ることができます。詳細については、「[D2C メッセージング](#d2c)」を参照してください。
    - *C2D メッセージの送信と、配信の確認メッセージの受信*。これらのエンドポイントにより、アプリケーション バックエンドは、信頼性の高い C2D メッセージを送信し、対応する配信または有効期限の確認メッセージを受信できます。詳細については、「[C2D メッセージング](#c2d)」を参照してください。
    - *ファイル通知の受信*。このメッセージング エンドポイントにより、デバイスがファイルを正常にアップロードしたときに通知を受信できます。

[IoT Hub の API と SDK][lnk-sdks] に関する記事で、これらのエンドポイントにアクセスするさまざまな方法が説明されています。

最後に注意が必要な重要事項として、すべての IoT Hub エンドポイントは、[TLS][lnk-tls] プロトコルを使用しています。また、暗号化されていない経路やセキュリティで保護されていない経路から公開されているエンドポイントはありません。

### Event Hubs と互換性のあるエンドポイントから読み取る方法<a id="eventhubcompatible"></a>

[Azure Service Bus SDK for .NET][lnk-servicebus-sdk] または [Event Hubs - イベント プロセッサ ホスト][lnk-eventprocessorhost]を使用する場合、適切なアクセス許可があれば任意の IoT Hub の接続文字列を使用することができます。これにより、**メッセージやイベント**を Event Hub の名前として使用できます。

IoT Hub を認識しない SDK (または製品の統合) を使用する場合は、[Azure ポータル][lnk-management-portal]の IoT Hub 設定から、Event Hubs と互換性のあるエンドポイントとイベント ハブの名前を取得する必要があります。

1. [IoT Hub] ブレードで、**[メッセージング]** をクリックします。
2. **[Device-to-cloud settings (D2C の設定)]** セクションに、**[Event Hub-compatible endpoint (イベント ハブと互換性のあるエンドポイント)]**、**[Event Hub-compatible name (イベント ハブと互換性のある名前)]**、**[パーティション]** の値があります。

    ![Device-to-cloud settings][img-eventhubcompatible]

> [AZURE.NOTE] SDK で **Hostname** または **Namespace** の値が必要な場合は、**[Event Hub-compatible endpoint (イベント ハブと互換性のあるエンドポイント)]** からスキームを削除します。たとえば、Event Hub 互換のエンドポイントが **sb://iothub-ns-myiothub-1234.servicebus.windows.net/** の場合、**Hostname** は **iothub-ns-myiothub-1234.servicebus.windows.net**、**Namespace** は **iothub-ns-myiothub-1234** です。

この場合、指定したイベント ハブに接続するための **ServiceConnect** のアクセス許可を持つ、共有アクセスのセキュリティ ポリシーを使用できます。

前の情報を使用してイベント ハブの接続文字列を作成する必要がある場合は、次のパターンを使用できます。

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

次に、IoT Hub から公開されている、Event Hub 互換のエンドポイントを使用できる SDK と統合の一覧を示します。

* [Event Hubs の Java クライアント](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm のスパウト](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。[スパウトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs)は GitHub で確認できます。
* [Apache Spark の統合](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## デバイス ID レジストリ

各 IoT Hub には、デバイス ID レジストリがあります。このレジストリを使用して、転送中の C2D メッセージを含むキューなど、サービスでデバイスごとのリソースを作成することができます。また、このレジストリを使用すると、「[アクセス制御](#accesscontrol)」セクションで説明するとおり、デバイス向けのエンドポイントへのアクセスを許可できます。

大まかに言うと、デバイス ID レジストリは、デバイス ID リソースの REST 対応のコレクションです。次のセクションでは、デバイス ID リソースのプロパティと、レジストリで ID に対して許可されている操作について詳しく説明します。

> [AZURE.NOTE] デバイス ID レジストリの操作に使用できる HTTP プロトコルと SDK の詳細については、[IoT Hub の API と SDK][lnk-sdks] に関する記事を参照してください。

### デバイス ID プロパティ<a id="deviceproperties"></a>

デバイス ID は、次のプロパティを持つ JSON ドキュメントとして表されます。

| プロパティ | オプション | Description |
| -------- | ------- | ----------- |
| deviceId | 必須、読み取り専用 (更新時) | ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| generationId | 必須、読み取り専用 | ハブによって生成された、大文字と小文字が区別される文字列 (最大 128 文字)。この文字列は、削除され、再作成された場合に、同じ **deviceId** を持つデバイスと区別するために使用します。 |
| etag | 必須、読み取り専用 | [RFC7232][lnk-rfc7232] に従い、デバイス ID の弱い ETag を表す文字列。|
| auth | 省略可能 | 認証情報とセキュリティのマテリアルを含む複合オブジェクト。 |
| auth.symkey | 省略可能 | base64 形式でプライマリ キーとセカンダリ キーを格納する複合オブジェクト。 |
| status | 必須 | アクセス インジケーター。**[有効]** か **[無効]** のいずれか。**[有効]** の場合、デバイスからの接続が許可されます。**[無効]** の場合、このデバイスからデバイス向けのエンドポイントにアクセスできません。 |
| statusReason | 省略可能 | デバイス ID の状態の理由を格納する、長さが 128 文字の文字列。すべての UTF-8 文字を使用できます。 |
| statusUpdateTime | 読み取り専用 | 状態が最後に更新された日時を示す時間のインジケーター。 |
| connectionState | 読み取り専用 | 接続状態を示すフィールド。**Connected** または **Disconnected** です。このフィールドは、デバイスの接続状態に関する IoT Hub ビューを表します。**重要**: このフィールドは、開発およびデバッグ専用として使用してください。接続状態は、MQTT または AMQP を使用するデバイスについてのみ更新されます。また、この更新はプロトコル レベルの ping (MQTT ping または AMQP ping) に基づいており、遅延は最大でもわずか 5 分です。このため、接続状態にあると報告されているものの実際には切断状態にあるデバイスのように、偽陽性を示す可能性があります。 |
| connectionStateUpdatedTime | 読み取り専用 | 前回接続状態が更新された日時を示す時間のインジケーター。 |
| lastActivityTime | 読み取り専用 | 前回デバイスが接続された日時またはメッセージを送受信した日時を示す時間のインジケーター。 |

> [AZURE.NOTE] 接続状態が表すのは、その接続状態を示す IoT Hub ビューのみです。ネットワークの状態と構成によっては、この状態の更新が遅延することがあります。

### デバイス ID の操作

IoT Hub のデバイス ID レジストリでは、次の操作が公開されています。

* デバイス ID の作成
* デバイス ID の更新
* ID ごとのデバイス ID の取得
* デバイス ID の削除
* 最大 1000 個の ID の一覧表示
* BLOB ストレージへのすべての ID のエクスポート
* BLOB ストレージからの ID のインポート

これらすべての操作で、[RFC7232][lnk-rfc7232] で指定されているオプティミスティック同時実行制御を使用できます。

> [AZURE.IMPORTANT] ハブの ID レジストリにあるすべての ID を取得する唯一の方法は、[エクスポート](#importexport)機能の使用です。

IoT Hub デバイス ID レジストリ:

- アプリケーションのメタデータは含まれていません。
- **deviceId** をキーとして使用することで、ディクショナリと同様にアクセスできます。
- 詳細なクエリはサポートされていません。

通常、IoT ソリューションにはソリューション固有のストアがあり、各ストアにはアプリケーション固有のメタデータが含まれています。たとえば、スマート ビルディング ソリューションのソリューション固有のストアでは、気温センサーをデプロイする部屋を記録します。

> [AZURE.IMPORTANT] デバイス ID レジストリは、デバイスの管理およびプロビジョニング操作でのみ使用します。実行時の高スループット操作が、デバイス ID レジストリの操作の影響を受けないようにする必要があります。たとえば、コマンドを送信する前に、デバイスの接続状態を確認するやり方は、サポートされていないパターンです。デバイス ID レジストリの[スロットル率](#throttling)と[デバイス ハートビート][lnk-guidance-heartbeat] パターンを必ず確認してください。

### デバイスの無効化

レジストリで ID の **status** プロパティを更新することにより、デバイスを無効にすることができます。通常、このプロパティは次の 2 つのシナリオで使用します。

- オーケストレーション プロセスのプロビジョニング中。詳細については、[「ソリューションの設計」の「デバイス プロビジョニング」][lnk-guidance-provisioning]を参照してください。
- 何らかの理由でデバイスが侵害された、または許可されていないと考えられる場合。

### デバイス ID のインポートとエクスポート <a id="importexport"></a>

[IoT Hub のリソース プロバイダーのエンドポイント](#endpoints)での非同期操作を使用して、IoT Hub の ID レジストリから一括でデバイス ID をエクスポートすることができます。エクスポートは、顧客が指定した BLOB コンテナーを使用して、デバイス ID レジストリから読み取ったデバイス ID データを保存する、長時間実行されるジョブです。

[IoT Hub のリソース プロバイダーのエンドポイント](#endpoints)での非同期操作を使用して、IoT Hub の ID レジストリに一括でデバイス ID をインポートすることができます。インポートは、顧客が指定した BLOB コンテナー内のデータを使用して、デバイス ID データをデバイス ID レジストリに書き込む、長時間実行されるジョブです。

- インポート API とエクスポート API の詳細については、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis]に関する記事を参照してください。
- インポートおよびエクスポート ジョブの実行方法の詳細については、「[IoT Hub デバイス ID の一括管理][lnk-bulk-identity]」を参照してください。

## セキュリティ<a id="security"></a>

このセクションでは、Azure IoT Hub をセキュリティで保護するためのオプションについて説明します。

### アクセス制御<a id="accesscontrol"></a>

IoT Hub では、次の一連の*アクセス許可*を使用して、各 IoT Hub のエンドポイントへのアクセスを許可します。次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

* **RegistryRead**。デバイス ID レジストリへの読み取りアクセス権を許可します。詳細については、「[デバイス ID レジストリ](#device-identity-registry)」を参照してください。
* **RegistryReadWrite**。デバイス ID レジストリへの読み取りと書き込みのアクセスを許可します。詳細については、「[デバイス ID レジストリ](#device-identity-registry)」を参照してください。
* **ServiceConnect**。クラウド サービス向けの通信エンドポイントと監視エンドポイントへのアクセスを許可します。たとえば、D2C メッセージの受信、C2D メッセージの送信、対応する配信確認メッセージの取得のアクセス許可をバックエンド クラウド サービスに付与します。
* **DeviceConnect**。デバイス向けの通信エンドポイントへのアクセスを許可します。たとえば、D2C メッセージの送信と、C2D メッセージの受信のアクセス許可を付与します。このアクセス許可はデバイスによって使用されます。

次の方法でアクセス許可を付与することができます。

* **ハブレベルの共有アクセス ポリシー**。共有アクセス ポリシーにより、前のセクションの一覧にあるアクセス許可を自由に組み合わせて付与できます。ポリシーは、[Azure ポータル][lnk-management-portal]で定義することも、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis] を使用してプログラムによって定義することもできます。新しく作成された IoT Hub には、次の既定のポリシーがあります。

    - **iothubowner**: すべてのアクセス許可を持つポリシー。
    - **service**: ServiceConnect アクセス許可を持つポリシー。
    - **device**: DeviceConnect アクセス許可を持つポリシー。
    - **registryRead**: RegistryRead アクセス許可を持つポリシー。
    - **registryReadWrite**: RegistryRead アクセス許可と RegistryWrite アクセス許可を持つポリシー。


* **デバイスごとのセキュリティ資格情報**。各 IoT Hub には、[デバイス ID レジストリ](#device-identity-registry)が含まれています。このレジストリ内の各デバイスでは、対応するデバイスのエンドポイントを対象として **DeviceConnect** アクセス許可を付与する、セキュリティ資格情報を構成できます。

たとえば、標準的な IoT ソリューションの場合は次のようになります。
- デバイス管理コンポーネントでは *registryReadWrite* ポリシーを使用します。
- イベント プロセッサ コンポーネントでは *service* ポリシーを使用します。
- ランタイム デバイス ビジネス ロジック コンポーネントでは *service* ポリシーを使用します。
- 個々のデバイスは、IoT Hub の ID レジストリに格納されている資格情報を使用して接続します。

IoT Hub のセキュリティに関するトピックのガイダンスについては、「[ソリューションの設計][lnk-guidance-security]」のセキュリティに関するセクションを参照してください。

### 認証

Azure IoT Hub では、共有アクセス ポリシーとデバイス ID レジストリのセキュリティ資格情報に対してトークンを確認することにより、エンドポイントへのアクセスを許可します。

対称キーなどのセキュリティの資格情報がネットワーク上で送信されることはありません。

> [AZURE.NOTE] Azure IoT Hub のリソース プロバイダーは、[Azure Resource Manager][lnk-azure-resource-manager] のすべてのプロバイダーと同様、Azure のサブスクリプションによりセキュリティで保護されています。

セキュリティ トークンを作成して使用する方法の詳細については、[IoT Hub セキュリティ トークン][lnk-sas-tokens]に関するページを参照してください。

#### プロトコルの詳細

MQTT、AMQP、および HTTP など、サポートされているプロトコルごとに、さまざまな方法でトークンが転送されます。


HTTP では、**Authorization** 要求ヘッダーに有効なトークンを含めることによって認証を実装します。


[AMQP][lnk-amqp] を使用する場合、[SASL PLAIN][lnk-sasl-plain] と [AMQP Claims-Based-Security][lnk-cbs] が IoT Hub でサポートされます。

AMQP Claims-Based-Security の場合、標準でこれらのトークンの送信方法が指定されます。

SASL PLAIN では、**ユーザー名**を以下のように指定できます。

* `{policyName}@sas.root.{iothubName}` (ハブレベルのトークンを使用する場合)。
* `{deviceId}` (デバイスを対象とするトークンを使用する場合)。

どちらの場合も、[IoT Hub セキュリティ トークン][lnk-sas-tokens]に関する記事で説明されているように、パスワード フィールドにトークンが含まれています。

MQTT を使用する場合、CONNECT パケットでは、ClientId、ユーザー名フィールドの {iothubhostname}/{deviceId}、およびパスワード フィールドの SAS トークンとして、deviceId が使用されます。{iothubhostname} は IoT Hub の完全な CName とする必要があります (contoso.azure-devices.net など)。

##### 例: #####

ユーザー名 (DeviceId では大文字と小文字が区別されます): `iothubname.azure-devices.net/DeviceId`

パスワード (デバイス エクスプローラーで SAS を生成します): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT Hub の SDK][lnk-sdks] を使用すると、サービスに接続した時点で自動的にトークンが生成されます。場合によっては、SDK でサポートされないプロトコルや認証方法もあります。

#### SASL PLAIN に関する特別な考慮事項

SASL PLAIN を使用する場合、IoT Hub に接続するクライアントは、TCP 接続ごとにトークンを 1 つ使用できます。トークンの有効期限が切れると、サービスから TCP 接続が切断され、再接続がトリガーされます。この動作は、アプリケーション バックエンド コンポーネントの場合は問題ありませんが、デバイス側アプリケーションの場合は、次の理由から大きな損害を及ぼす可能性があります。

*  ゲートウェイは、通常多くのデバイスの代理として接続しています。SASL PLAIN を使用する場合、IoT Hub に接続するデバイスごとに、個別の TCP 接続を作成する必要があります。このシナリオにより、電源とネットワーク リソースの消費量が大幅に増大し、各デバイスの接続の待機時間が増加します。
* 各トークンの有効期限が切れた後に、再接続に使用するリソースの量が増加すると、リソースが限られたデバイスには悪影響が出ます。

### ハブレベルの資格情報のスコープ

制限付きのリソース URI を持つトークンを作成することにより、ハブレベルのセキュリティ ポリシーのスコープを指定できます。たとえば、デバイスからの D2C メッセージを送信するエンドポイントは **/devices/{deviceId}/messages/events** になります。また、**DeviceConnect** アクセス許可を持つハブレベルの共有アクセス ポリシーを使用して、resourceURI が **/devices/{deviceId}** であるトークンに署名することもできます。この方法により、デバイスの **deviceId** の代わりにデバイスを送信するためにのみ使用できるトークンが作成されます。

これは [Event Hubs の発行元ポリシー][lnk-event-hubs-publisher-policy]に似たメカニズムであり、カスタムの認証方法の実装を可能にします。詳細については、「[ソリューションの設計][lnk-guidance-security]」のセキュリティに関するセクションを参照してください。

## メッセージング

IoT Hub には、通信を行うためのメッセージング プリミティブが用意されています。

- [C2D](#c2d): アプリケーション バックエンド (*service* または *cloud*) から。
- [D2C](#d2c): デバイスからアプリケーション バックエンドへ。
- [ファイルのアップロード](#fileupload): デバイスから関連付けられている Azure ストレージ アカウントへ。

IoT Hub のメッセージング機能の中心となる特性は、メッセージの信頼性と持続性です。これらのプロパティにより、デバイス側では断続的な接続に対する復元性を、クラウド側ではイベント処理の負荷の急増に対する復元性を実現できます。IoT Hub では、D2C および C2D メッセージングの両方について、*少なくとも 1 回*の配信保証が実装されます。

IoT Hub では、(MQTT、AMQP、HTTP などの) デバイス用のプロトコルが複数サポートされています。プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルでサポートされる共通のメッセージ形式が定義されます。

### メッセージの形式<a id="messageformat"></a>

IoT Hub のメッセージは、次の要素で構成されています。

* 一連の*システム プロパティ*。IoT Hub が解釈または設定するプロパティです。この設定は、事前に決定されています。
* 一連の*アプリケーション プロパティ*。これは、メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。IoT Hub によってこれらのプロパティが変更されることはありません。
* 非透過的なバイナリ本文。

さまざまなプロトコルでメッセージをエンコードする方法の詳細については、[IoT Hub の API と SDK][lnk-sdks] に関する記事を参照してください。

これは、IoT Hub メッセージ内の一連のシステム プロパティです。

| プロパティ | Description |
| -------- | ----------- |
| MessageId | 要求/応答パターンに使用する、メッセージのユーザー設定 ID。形式: ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| Sequence number | IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |
| To | [C2D](#c2d) メッセージで指定される宛先。 |
| ExpiryTimeUtc | メッセージの有効期限の日時。 |
| EnqueuedTime | IoT Hub でメッセージを受信した日時。 |
| CorrelationId | 通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 |
| UserId | メッセージの送信元を指定するために使用される ID。IoT Hub でメッセージが生成されると、`{iot hub name}` に設定されます。 |
| Ack | フィードバック メッセージのジェネレーター。このプロパティは、デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用されます。使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値を意味します。詳細については、「[メッセージのフィードバック](#feedback)」を参照してください。 |
| ConnectionDeviceId | IoT Hub で D2C メッセージに対して設定される ID。メッセージを送信したデバイスの **deviceId** が含まれます。 |
| ConnectionDeviceGenerationId | IoT Hub で D2C メッセージに対して設定される ID。([デバイス ID のプロパティ](#deviceproperties)に従って) メッセージを送信したデバイスの **generationId** が含まれています。 |
| ConnectionAuthMethod | IoT Hub で D2C メッセージに対して設定される認証方法。このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。詳細については、[D2C のなりすまし対策](#antispoofing)に関する記述を参照してください。|

### 通信プロトコルの選択 <a id="amqpvshttp"></a>

IoT Hub では、デバイス側の通信に MQTT、 [AMQP][lnk-amqp]、AMQP over WebSockets、および HTTP/1 の各プロトコルをサポートしています。プロトコル選択に関する高度な推奨事項には次のようなものがあります。

| プロトコル | そのプロトコルを選択しなければならない場合 |
| -------- | ------------------------------------ |
| MQTT | WebSocket を使用する必要がないすべてのデバイスで使用します。 |
| AMQPS | デバイスを多重化する接続を活用するために、フィールド ゲートウェイとクラウド ゲートウェイで使用します。<br/> ポート 443 に接続する必要がある場合に使用します。 |
| HTTPS | その他のプロトコルをサポートできないデバイスに使用します。 |

デバイス側の通信用プロトコルを選択する場合、次の点を考慮してください。

* **C2D のパターン**。HTTP/1 には、サーバー プッシュを実装する効率的な方法がありません。そのため、HTTP/1 を使用する場合、デバイスは IoT Hub に対して C2D メッセージのポーリングを行います。この方法は、デバイスと IoT Hub の両方できわめて非効率的です。現在の HTTP/1 ガイドラインでは、各デバイスによるメッセージのポーリングの間隔は 25 分以上となっています。一方、MQTT と AMQP では、C2D メッセージを受信する場合のサーバー プッシュがサポートされています。IoT Hub からデバイスへのメッセージも即座にプッシュできます。配信の待機時間が問題となる場合は、AMQP または MQTT が使用に最適なプロトコルです。頻繁に接続されないデバイスの場合は、HTTP/1 でも対応できます。
* **フィールド ゲートウェイ**。HTTP/1 と MQTT を使用する場合は、同じ TLS 接続で複数のデバイス (それぞれが、デバイス独自の資格情報を有する) を接続することはできません。したがって、[フィールド ゲートウェイ シナリオ][lnk-azure-gateway-guidance]の場合は、フィールド ゲートウェイに接続するデバイスごとにフィールド ゲートウェイと IoT Hub の間に TLS 接続が 1 つ必要となるため、これらのプロトコルは次善の策です。
* **リソースの少ないデバイス**。MQTT および HTTP/1 ライブラリのフットプリントは、AMQP ライブラリの場合より小さくなります。そのため、リソースが限られたデバイス (RAM が 1 MB 未満など) の場合、MQTT と HTTP/1 が実装可能な唯一のプロトコルとなります。
* **ネットワーク トラバーサル**。MQTT Standard はポート 8883 でリッスンします。このため、ネットワークが HTTP 以外のプロトコルに限定されている場合、問題が発生する可能性があります。HTTP と AMQP (over WebSockets) の両方がこのシナリオで使用できます。
* **ペイロードのサイズ**。AMQP と MQTT は、バイナリ プロトコルであり、HTTP/1 よりペイロードがはるかにコンパクトです。

> [AZURE.NOTE] HTTP/1 を使用する場合、各デバイスでは 25 分以上の間隔で C2D メッセージをポーリングする必要があります。ただし、開発段階では、ポーリングの頻度を 25 分より短く設定することができます。

<a id="mqtt-support">
#### MQTT サポートに関する留意事項
IoT Hub では、次の制限事項と特定の動作で MQTT v3.1.1 プロトコルを実装します。

  * **QoS 2 はサポートされていません**。デバイス クライアントが **QoS 2** によってメッセージを発行すると、IoT Hub はネットワーク接続を閉じます。デバイス クライアントが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。
  * **メッセージの保持は維持されません**。デバイス クライアントが RETAIN フラグを 1 に設定してメッセージを発行すると、IoT Hub はそのメッセージに **x-opt-retain** アプリケーション プロパティを追加します。この場合は、IoT Hub は保持メッセージを保持するのでなく、バックエンド アプリケーションに渡します。

詳細については、「[IoT Hub の MQTT サポート][lnk-mqtt-support]」を参照してください。

最後の考慮事項として、[Azure IoT Hub プロトコル ゲートウエイ][lnk-azure-protocol-gateway]に関するページを確認してください。これにより、IoT Hub と直接やり取りする高性能のカスタム プロトコル ゲートウェイをデプロイできます。Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。ただし、このアプローチでは、カスタム プロトコル ゲートウェイを自分でホストし、運用する必要があります。

### D2C (デバイスからクラウド)<a id="d2c"></a>

「[エンドポイント](#endpoints)」セクションで詳しく説明したとおり、D2C メッセージはデバイス向けエンドポイント (**/devices/{deviceId}/messages/events**) を介して送信され、[Event Hubs][lnk-event-hubs] と互換性のあるサービス向けエンドポイント (**/messages/events**) を介して受信されます。Standard Event Hubs の統合と SDK を使用して D2C メッセージを受信できます。

IoT Hub は、[Event Hubs][lnk-event-hubs] と類似した方法で D2C メッセージングを実装します。IoT Hub の D2C メッセージは、[Service Bus][lnk-servicebus] の*メッセージ*よりも Event Hubs の*イベント*に類似しています。

この実装には、次のような意味があります。

* Event Hubs のイベントと同様に、D2C メッセージには持続性があり、最長で 7 日間 IoT Hub に保持されます (「[D2C の構成オプション](#d2cconfiguration)」を参照してください)。
* D2C メッセージは、作成時に設定されるパーティションの固定セットにまたがって分割されます (「[D2C の構成オプション](#d2cconfiguration)」を参照してください)。
* Event Hubs と同様に、D2C メッセージを読み取るクライアントでは、パーティションと、チェックポイントを処理する必要があります。[Event Hubs - イベントの発行][lnk-event-hubs-consuming-events]に関するページを参照してください。
* Event Hubs のイベントと同様、D2C メッセージのサイズは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。バッチのサイズは最大で 256 KB、メッセージの数は最大 500 個です。

ただし、IoT Hub の D2C のメッセージングと Event Hubs には、いくつかの重要な違いがあります。

* 「[セキュリティ](#security)」セクションで説明したように、IoT Hub では、デバイスごとの認証とアクセス制御が許可されています。
* IoT Hub では、何百万ものデバイスを同時に接続できますが (「[クォータとスロットル](#throttling)」を参照してください)、Event Hubs では、名前空間ごとの AMQP 接続が 5000 に制限されています。
* IoT Hub では、**PartitionKey** を使用した任意のパーティション分割は許可されていません。D2C メッセージは、発信元の **deviceId** に基づいてパーティション分割されます。
* IoT Hub のスケーリングは Event Hubs と若干異なります。詳細については、「[IoT Hub のスケーリング][lnk-guidance-scale]」を参照してください。

上記の説明は、すべてのシナリオで IoT Hub が Event Hubs に代わるという意味ではないので、ご注意ください。たとえば、一部のイベントの計算処理では、データ ストリームを分析する前に、別のプロパティまたはフィールドを基準にイベントを再パーティション分割する必要があります。このシナリオでは、Event Hub を使用して、ストリーム処理パイプラインの 2 つの部分を分離できます。詳細については、「[Azure Event Hubs の概要][lnk-eventhub-partitions]」の「*パーティション*」を参照してください。

D2C メッセージングの使用方法の詳細については、[IoT Hub の API と SDK][lnk-sdks] に関する記事を参照してください。

> [AZURE.NOTE] HTTP を使用して D2C メッセージを送信する場合、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。

#### 非テレメトリ トラフィック

多くの場合、デバイスからは、テレメトリ データ ポイントが送信されるだけでなく、アプリケーションのビジネス ロジック層での実行と処理を必要とするメッセージと要求も送信されます。たとえば、バックエンドで特定のアクションをトリガーする必要がある重大なアラートや、バックエンドから送信されるコマンドに対するデバイスの応答などです。

このようなメッセージを処理する最適な方法の詳細については、[D2C の処理][lnk-guidance-d2c-processing]に関するページを参照してください。

#### D2C の構成オプション<a id="d2cconfiguration"></a>

IoT Hub は、D2C メッセージングを制御できる次のプロパティを公開しています。

* **パーティション数**。このプロパティは作成時に設定し、D2C イベントを取り込む場合のパーティション数を定義します。
* **保存期間**。このプロパティでは、D2C メッセージのリテンション期間を指定します。既定は 1 日ですが、7 日間に増やすことができます。

また Event Hubs と同様、IoT Hub でも、D2C の受信エンドポイントでコンシューマー グループを管理できます。

これらのプロパティはすべて、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis] を使用してプログラムにより変更するか、[Azure ポータル][lnk-management-portal]を使用して変更できます。

#### なりすまし対策のプロパティ<a id="antispoofing"></a>

D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

最初の 2 つには、「[デバイス ID プロパティ](#deviceproperties)」に従い、発信元デバイスの **deviceId** と **generationId** が含まれています。

**ConnectionAuthMethod** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### C2D (クラウドからデバイス) <a id="c2d"></a>

「[エンドポイント](#endpoints)」セクションで詳しく説明したとおり、C2D メッセージはサービス向けエンドポイント (**/messages/devicebound**) を介して送信できます。デバイスはそのメッセージをデバイス固有のエンドポイント (**/devices/{deviceId}/messages/devicebound**) を介して受信できます。

個々の C2D メッセージの宛先は単一のデバイスであり、**to** プロパティが **/devices/{deviceId}/messages/devicebound** に設定されます。

>[AZURE.IMPORTANT] 各デバイスのキューで保持できる C2D メッセージは最大 50 個です。同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。

> [AZURE.NOTE] C2D メッセージを送信するとき、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。

#### メッセージのライフサイクル<a id="message lifecycle"></a>

少なくとも 1 回のメッセージ配信の保証を実装するために、C2D メッセージはデバイスごとのキューに保持されます。IoT Hub がキューからメッセージを削除できるように、デバイスで明示的に*完了*を確認する必要があります。そうすることで、接続とデバイスの障害に対する復元性が保証されます。

次の図に、C2D メッセージのライフサイクルの状態に関するグラフを示します。

![クラウドとデバイス間のメッセージのライフ サイクル][img-lifecycle]

サービスからメッセージが送信されると、そのメッセージは*エンキューされた*と見なされます。デバイスでメッセージを*受信*する場合、同じデバイス上の他のスレッドが他のメッセージの受信を開始できるようにするために、IoT Hub によりメッセージが*ロック*されます (状態は**非表示**に設定されます)。デバイスのスレッドがメッセージの処理を完了すると、メッセージを*完了*して IoT Hub にその旨を通知します。

デバイスは次の動作をすることもあります。

- メッセージの*拒否*。この場合、IoT Hub によってメッセージの状態が**配信不能**に設定されます。
- メッセージの*破棄*。この場合、IoT Hub によってメッセージがキューに戻され、状態が**エンキュー**に設定されます。

スレッドが IoT Hub に通知することなく、メッセージの処理に失敗することもあります。その場合、*表示 (またはロック) がタイムアウト*になった後で、メッセージの状態が自動的に**非表示**から**エンキュー**に切り替わります。このタイムアウトの既定値は 1 分です。

メッセージの状態は**エンキュー**と**非表示**の間で切り替わりますが、この回数が IoT Hub の**最大配信数**のプロパティで指定された上限に達すると、IoT Hub によってメッセージの状態が**配信不能**に設定されます。同様に、有効期限 ([有効期限](#ttl)に関するセクションを参照) が切れた後も、IoT Hub によってメッセージの状態が**配信不能**に設定されます。

C2D メッセージのチュートリアルについては、[Azure IoT Hub C2D メッセージの使用][lnk-getstarted-c2d-tutorial]に関する記事を参照してください。さまざまな API と SDK が C2D 機能を公開する方法に関するリファレンス トピックについては、[IoT Hub API と SDK][lnk-sdks] に関する記事を参照してください。

> [AZURE.NOTE] 通常、メッセージの損失がアプリケーション ロジックに影響しない場合、C2D メッセージは必ず完了されます。たとえば、メッセージの内容が正常にローカル ストレージで保持されている場合、操作が正常に実行された場合などがあります。また、メッセージには、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報が含まれている場合もあります。実行時間の長いタスクの場合、タスクの説明をローカル ストレージに保持した後で C2D メッセージを完了できます。その後、タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによりアプリケーション バックエンドに通知できます。

#### メッセージの有効期限 <a id="ttl"></a>

すべての C2D メッセージに有効期限があります。これはサービス (**ExpiryTimeUtc** プロパティ) で明示的に設定するか、IoT Hub のプロパティとして指定した既定の*有効期限*を使用して IoT Hub で設定できます。「[C2D の構成オプション](#c2dconfiguration)」を参照してください。

> [AZURE.NOTE] メッセージの有効期限を利用する一般的な方法は、有効期限に短い値を設定して、接続されていないデバイスにメッセージが送信されないようにするというものです。この方法を使用すると、デバイスの接続状態を維持するのと同じ結果が得られますが、大幅に効率化されます。メッセージの受信確認を要求することにより、メッセージを受信できるデバイスや、オンラインではないデバイスまたは障害が発生しているデバイスについて IoT Hub から通知を受け取ることもできます。

#### メッセージのフィードバック<a id="feedback"></a>

C2D メッセージを送信するときに、サービスは、そのメッセージの最終状態に関するメッセージごとのフィードバックの配信を要求できます。

- **Ack** プロパティを **positive** に設定すると、C2D メッセージの状態が**完了**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。
- **Ack** プロパティを **negative** に設定すると、C2D メッセージの状態が**配信不能**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。
- **Ack** プロパティを **full** に設定すると、上記のどちらの場合にも IoT Hub によりフィードバック メッセージが生成されます。

> [AZURE.NOTE] **Ack** が **full** の場合で、フィードバック メッセージを受信しない場合は、フィードバック メッセージの有効期限が切れたことを意味します。このとき、元のメッセージがどうなったかをサービスが認識することはできません。実際には、有効期限切れになる前にフィードバックをサービスが確実に処理できることが必要です。有効期限は最長 2 日間であるため、障害が発生した場合も、サービスを再び稼働させる時間は十分にあります。

「[エンドポイント](#endpoints)」で説明したように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/feedback**) 経由で、メッセージとしてフィードバックを配信します。フィードバックを受信するためのセマンティクスは C2D メッセージの場合と同様であり、[メッセージのライフサイクル](#message lifecycle)も同じです。可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

デバイスによってフィードバックのエンドポイントから取得された各メッセージには、次のプロパティがあります。

| プロパティ | Description |
| -------- | ----------- |
| EnqueuedTime | メッセージが作成された日時を示すタイムスタンプ。 |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | メッセージの結果が発生した日時を示すタイムスタンプ。たとえば、デバイスの完了やメッセージの期限切れなどです。 |
| OriginalMessageId | このフィードバック情報が関連付けられている C2D メッセージの **MessageId** です。 |
| StatusCode | 必須の整数です。IoT Hub によって生成されたフィードバック メッセージで使用されます。<br/> 0 = 成功しました。<br/> 1 = メッセージの有効期限が切れました。<br/> 2 = 最大配信数を超えました。<br/> 3 = メッセージは拒否されました。 |
| Description | **StatusCode** の文字列値。 |
| DeviceId | フィードバックのこの部分が関連付けられている C2D メッセージの宛先デバイスの **DeviceId**。 |
| DeviceGenerationId | フィードバックのこの部分が関連付けられている C2D メッセージの宛先デバイスの **DeviceGenerationId**。 |


>[AZURE.IMPORTANT] メッセージのフィードバックを元のメッセージと関連付けることができるように、サービスは C2D メッセージの **MessageId** を指定する必要があります。

次の例は、フィードバック メッセージの本文を示しています。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### C2D の構成オプション<a id="c2dconfiguration"></a>

各 IoT Hub では、C2D メッセージング用に次の構成オプションを公開しています。

| プロパティ | Description | 範囲と既定値 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | C2D メッセージの TTL の既定値。 | 最大 2D の ISO\_8601 書式による間隔 (最小 1 分)。既定値: 1 時間。 |
| maxDeliveryCount | デバイスごとの C2D キューの最大配信数。 | 1 ～ 100。既定値: 10。 |
| feedback.ttlAsIso8601 | サービス宛てのフィードバックのメッセージの保有期間。 | 最大 2D の ISO\_8601 書式による間隔 (最小 1 分)。既定値: 1 時間。 |
| feedback.maxDeliveryCount | フィードバック キューの最大配信数。 | 1 ～ 100。既定値: 100。 |

詳細については、[IoT Hub の管理][lnk-portal]に関するページを参照してください。

### ファイルのアップロード <a id="fileupload"></a>

「[エンドポイント](#endpoints)」セクションで詳しく説明したとおり、デバイスは、デバイス向けエンドポイント (**/devices/{deviceId}/files**) を介して通知を送信することで、ファイルのアップロードを開始できます。デバイスが IoT Hub にアップロードの完了を通知すると、IoT Hub は、サービス向けエンドポイント (**/messages/servicebound/filenotifications**) 経由でメッセージとして受信できるファイルのアップロード通知を生成します。

IoT Hub 自体を介してメッセージをやり取りする代わりに、IoT Hub が、関連付けられている Azure ストレージ アカウントへのディスパッチャーとして機能します。デバイスは、アップロードするファイルに固有のストレージ トークンを IoT Hub に要求します。SAS URI を使用して、ファイルをストレージにアップロードし、アップロードが完了すると、IoT Hub に完了の通知を送信します。IoT Hub は、ファイルがアップロードされたことを確認すると、新しいサービス向けファイル通知メッセージング エンドポイントにファイルのアップロード通知を追加します。

#### Azure ストレージ アカウントと IoT Hub の関連付け

ファイルのアップロード機能を使用するには、最初に Azure ストレージ アカウントを IoT Hub にリンクする必要があります。[Azure ポータル][lnk-management-portal]からリンクするか、[Azure IoT Hub のリソース プロバイダー API][lnk-resource-provider-apis] からプログラムによりリンクできます。ストレージ アカウントと IoT Hub を関連付けると、デバイスがファイルのアップロード要求を開始したときに、サービスがデバイスに SAS URI を返します。

> [AZURE.NOTE] [Azure IoT Hub SDK][lnk-sdks] では、SAS URI の取得、ファイルのアップロード、および IoT Hub へのアップロード完了の通知を自動的に処理します。

#### ファイルのアップロードの初期化

IoT Hub には、ファイルのアップロードをサポートする 2 つの REST エンドポイントがあります。1 つは、ストレージの SAS URI を取得します。もう 1 つは、アップロードの完了を IoT Hub に通知します。デバイスは、`{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` で IoT Hub に GET を送信することで、ファイルのアップロード処理を開始します。IoT Hub は、アップロードするファイルに固有の SAS URI と、アップロードの完了後に使用する相関 ID を返します。

#### IoT Hub へのファイルのアップロード完了の通知

デバイスは、Azure Storage SDK を使用してストレージにファイルをアップロードします。アップロードが完了すると、デバイスは最初の GET で受け取った相関 ID を使用して `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications/{correlationId}` で IoT Hub に POST を送信します。

#### ファイルのアップロード通知

デバイスがファイルをアップロードし、アップロード完了を IoT Hub に通知すると、サービスは必要に応じてファイルの名前と保存場所を含む通知メッセージを生成します。

「[エンドポイント](#endpoints)」で説明したように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/fileuploadnotifications**) 経由でメッセージとしてファイルのアップロード通知を配信します。ファイルのアップロード通知の受信セマンティクスは C2D メッセージの場合と同様であり、[メッセージのライフサイクル](#message lifecycle)も同じです。ファイルのアップロード通知エンドポイントから取得した各メッセージは、次のプロパティを持つ JSON レコードです。

| プロパティ | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | 通知が作成された日時を示すタイムスタンプ。 |
| DeviceId | ファイルをアップロードしたデバイスの **DeviceId**。 |
| BlobUri | アップロードされたファイルの URI。 |
| BlobName | アップロードされたファイルの名前。 |
| LastUpdatedTime | ファイルが最後に更新された日時を示すタイムスタンプ。 |
| BlobSizeInBytes | アップロードされたファイルのサイズ。 |

**例**。ファイルのアップロード通知メッセージの本文の例を次に示します。

```
{
	"deviceId":"mydevice",
	"blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
	"blobName":"mydevice/myfile.jpg",
	"lastUpdatedTime":"2016-06-01T21:22:41+00:00",
	"blobSizeInBytes":1234,
	"enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

#### ファイルのアップロード通知の構成オプション <a id="c2dconfiguration"></a>

各 IoT Hub では、ファイルのアップロード通知用に次の構成オプションを公開しています。

| プロパティ | Description | 範囲と既定値 |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | ファイルのアップロード通知をファイル通知エンドポイントに書き込むかどうかを制御します。 | ブール値。既定値: True。 |
| **fileNotifications.ttlAsIso8601** | ファイルのアップロード通知の既定の TTL。 | 最大 48 時間の ISO\_8601 書式による間隔 (最小 1 分)。既定値: 1 時間。 |
| **fileNotifications.lockDuration** | ファイルのアップロード通知キューのロック期間。 | 5 ～ 300 秒 (最小 5 秒)。既定値: 60 秒。 |
| **fileNotifications.maxDeliveryCount** | ファイルのアップロード通知キューの最大配信数。 | 1 ～ 100。既定値: 100。 |

詳細については、[IoT Hub の管理][lnk-portal]に関するページを参照してください。

## クォータとスロットル<a id="throttling"></a>

各 Azure サブスクリプションに最大 10 個の IoT Hub を割り当てることができます。

各 IoT Hub は、特定の SKU のユニット数でプロビジョニングされます (詳細については、[Azure IoT Hub の価格][lnk-pricing]に関するページを参照してください)。SKU とユニット数により、送信できるメッセージの1 日あたりの最大クォータが決定されます。

また、IoT Hub がすべての操作に適用するスロットル制限は、SKU によっても決まります。

### 操作のスロットル

操作のスロットルは、ごく限られた範囲に適用される、不正使用を回避するためのレート制限です。IoT Hub は、可能な限りエラーを返さないようにしようとしますが、長時間にわたりスロットル違反が続く場合は、例外が返され始めます。

適用されるスロットルの一覧を次に示します。値は個々のハブのものです。

| スロットル | ハブごとの値 |
| -------- | ------------- |
| ID レジストリの操作 (作成、取得、一覧表示、更新、削除) | 5000/分/ユニット (S3 の場合) <br/> 100/分/ユニット (S1 および S2 の場合)。 |
| デバイスの接続 | 6000/秒/ユニット (S3 の場合)、120/秒/ユニット (S2 の場合)、12/秒/ユニット (S1 の場合)。<br/>最小 100/秒。<br/> たとえば、2 ユニットの S1 では 2 x 12 = 24/秒ですが、ユニット全体では少なくとも 100/秒となります。9 ユニットの S1 の場合、ユニット全体で 108/秒 (9 * 12) となります。 |
| デバイスからクラウドへの送信 | 6000/秒/ユニット (S3 の場合)、120/秒/ユニット (S2 の場合)、12/秒/ユニット (S1 の場合)。<br/>最小 100/秒。<br/> たとえば、2 ユニットの S1 では 2 x 12 = 24/秒ですが、ユニット全体では少なくとも 100/秒となります。9 ユニットの S1 の場合、ユニット全体で 108/秒 (9 * 12) となります。 |
| クラウドからデバイスへの送信 | 5000/分/ユニット (S3 の場合)、100/分/ユニット (S1 と S2 の場合)。 |
| クラウドからデバイスへの受信 | 50000/分/ユニット (S3 の場合)、1000/分/ユニット (S1 と S2 の場合)。 |
| ファイルのアップロード操作 | 5000 ファイルのアップロード通知/分/ユニット (S3 の場合)、100 ファイルのアップロード通知/分/ユニット (S1 と S2 の場合)。<br/> 1 つのストレージ アカウントに対して 10000 個の SAS URI を一度に提供できます。<br/> デバイスあたり 10 個の SAS URI を一度に提供できます。 | 

*デバイスの接続*スロットルは、同時に接続されたデバイスの最大数ではなく、新しいデバイス接続を IoT Hub を使用して確立できる割合を制御するものであることを明確にしておくことが重要です。スロットルは、ハブにプロビジョニングされたユニット数に依存します。

たとえば、1 つの S1 ユニットを購入した場合、1 秒あたり 100 接続のスロットルを利用できます。これは、100,000 個のデバイスに接続するために、少なくとも 1,000 秒 (約 16 分) かかることを意味します。ただし、デバイス ID レジストリに登録されたデバイスの数だけ、同時に接続されたデバイスを持つことができます。

IoT Hub スロットルの動作の詳細については、ブログ投稿「[IoT Hub throttling and you (IoT Hub スロットルの操作)][lnk-throttle-blog]」を参照してください。

>[AZURE.NOTE] 任意の時点で、IoT Hub にプロビジョニングされたユニットを増やすことで、クォータやスロットルの制限値を増やすことができます。

>[AZURE.IMPORTANT] ID レジストリの操作は、デバイスの管理とプロビジョニングのシナリオにおける実行時の使用を目的としています。多数のデバイス ID の読み取りまたは更新は、[インポート/エクスポート ジョブ](#importexport)でサポートされています。

## 次のステップ

IoT Hub の開発の概要については以上です。詳細については、以下のページをご覧ください。

- [デバイスからのファイルのアップロード (チュートリアル)][lnk-file upload]
- [プログラムによる IoT Hub 作成][lnk-create-hub]
- [C SDK の概要][lnk-c-sdk]
- [IoT Hub SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]
- [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]



[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0907_2016-->