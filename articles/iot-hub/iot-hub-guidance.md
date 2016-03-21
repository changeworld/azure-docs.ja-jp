<properties
 pageTitle="IoT Hub に関するガイダンス | Microsoft Azure"
 description="Azure IoT Hub を使用して IoT ソリューションを開発するためのゲートウェイ、デバイス プロビジョニング、認証に関するガイダンス トピックです。"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# ソリューションの設計

この記事では、モノのインターネット (IoT) ソリューションで次の機能を設計する方法について説明します。

- デバイス プロビジョニング
- フィールド ゲートウェイ
- デバイスの認証

## デバイス プロビジョニング

IoT ソリューションでは、次のような個々のデバイスに関するデータを格納します。

- デバイスの ID と認証キー
- デバイス ハードウェアの種類とバージョン
- デバイスの状態
- ソフトウェアのバージョンと機能
- デバイス コマンドの履歴

特定の IoT ソリューションに格納されるデバイス データは、そのソリューションの具体的な要件によって異なりますが、ソリューションには少なくともデバイスの ID と認証キーを格納する必要があります。Azure IoT Hub には、ID、認証キー、状態コードなど、各デバイスの値を格納できる [ID レジストリ][lnk-devguide-identityregistry]があります。ソリューションでは、テーブル、BLOB、Azure DocumentDB など、その他の Azure サービスを使用して、追加のデバイス データを格納できます。

*デバイス プロビジョニング*とは、最初のデバイス データをソリューション内のストアに追加するプロセスです。新しいデバイスをハブに接続できるようにするには、新しいデバイスの ID とキーを [IoT Hub ID レジストリ][lnk-devguide-identityregistry]に追加する必要があります。プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。

[IoT Hub デバイス管理のガイダンス][lnk-device-management]に関する記事では、デバイス プロビジョニングの一般的な方法について説明します。[IoT Hub ID レジストリ API][lnk-devguide-identityregistry] を使用すると、IoT Hub をプロビジョニング プロセスに統合できます。

## フィールド ゲートウェイ

IoT ソリューションでは、*フィールド ゲートウェイ*はデバイスと IoT Hub の間に配置され、通常はデバイスの近くにあります。デバイスは、そのデバイスでサポートされているプロトコルを使用してフィールド ゲートウェイと直接通信します。フィールド ゲートウェイは、IoT Hub でサポートされているプロトコルを使用して IoT Hub と通信します。フィールド ゲートウェイには、専用のスタンドアロン デバイスを使用することも、既存のハードウェアで動作するソフトウェアを使用することもできます。

フィールド ゲートウェイは、通常、ソリューションにおけるアクセスと情報フローの管理において大きな役割を果たすため、単純なトラフィック ルーティング デバイス (ネットワーク アドレス変換 (NAT) デバイスやファイアウォールなど) とは異なります。たとえば、フィールド ゲートウェイでは次のことができます。

- ローカル デバイスを管理する。たとえば、フィールド ゲートウェイは、イベント ルールの処理を実行したり、特定のテレメトリ データに応答してデバイスにコマンドを送信したりできます。
- テレメトリ データを IoT Hub に転送する前にフィルター処理または集計する。これにより、IoT Hub に送信されるデータの量を減らし、場合によってはソリューションのコストを削減することができます。
- デバイスのプロビジョニングを支援する。
- テレメトリ データを変換し、ソリューションのバックエンドでの処理を容易にする。
- プロトコルの変換を実行し、デバイスが IoT Hub でサポートされているトランスポート プロトコルを使用しない場合でも IoT Hub と通信できるようにする。

> [AZURE.NOTE] 通常は、フィールド ゲートウェイをデバイスのローカルにデプロイしますが、シナリオによっては、クラウドに[プロトコル ゲートウェイ][lnk-gateway]をデプロイする場合もあります。

### フィールド ゲートウェイのタイプ

フィールド ゲートウェイは、*透過的*または*非透過的*にすることができます。

| &nbsp; | 透過的なゲートウェイ | 非透過的なゲートウェイ|
|--------|-------------|--------|
| IoT Hub ID レジストリに格納される ID | 接続されているすべてのデバイスの ID | フィールド ゲートウェイの ID のみ |
| IoT Hub が提供する[デバイスID なりすまし対策][lnk-devguide-antispoofing] | あり | なし |
| [スロットルとクォータ][lnk-throttles-quotas] | 各デバイスに適用 | フィールド ゲートウェイに適用 |

> [AZURE.IMPORTANT]  非透過的なゲートウェイ パターンを使用する場合、そのゲートウェイ経由で接続するデバイスはすべて、最大で 50 個のメッセージを格納できる同じ C2D キューを共有します。したがって、非透過的なゲートウェイ パターンを使用するのは、各フィールド ゲートウェイを介して接続しているデバイスがほとんどなく、C2D トラフィックが少ない場合に限定する必要があります。

### その他の考慮事項

[Azure IoT デバイス SDK][lnk-device-sdks] を使用してフィールド ゲートウェイを実装できます。一部のデバイス SDK には、複数のデバイスから同じ接続を経由した IoT Hub への通信を多重化する機能など、フィールド ゲートウェイの実装に役立つ固有の機能が用意されています。[IoT Hub 開発者ガイドの「通信プロトコルの選択」][lnk-devguide-protocol]で説明されているように、フィールド ゲートウェイのトランスポート プロトコルとして HTTP/1 を使用しないでください。

## カスタム デバイスの認証

IoT Hub の[デバイス ID レジストリ][lnk-devguide-identityregistry]を使用して、デバイスごとのセキュリティ資格情報とアクセス制御を構成できます。ただし、IoT ソリューションで既にカスタム デバイス ID レジストリや認証スキームにかなり投資している場合、*トークン サービス*を作成すると、この既存のインフラストラクチャを IoT Hub と統合できます。この方法により、ソリューションで他の IoT 機能を使用できます。

トークン サービスはカスタム クラウド サービスの 1 つです。このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub *共有アクセス ポリシー*を使用して、*デバイスを対象とする*トークンを作成します。これらのトークンにより、デバイスは IoT Hub に接続できるようになります。

  ![Steps of the token service pattern][img-tokenservice]

トークン サービス パターンの主な手順を次に示します。

1. [IoT Hub 共有アクセス ポリシー][lnk-devguide-security]を作成し、IoT Hub に対する **DeviceConnect** アクセス許可を指定します。このポリシーは、[Azure ポータル][lnk-portal]またはプログラムで作成できます。トークン サービスは、このポリシーを使用して、作成されるトークンに署名します。
2. IoT Hub にアクセスする必要があるデバイスは、トークン サービスに署名付きトークンを要求します。デバイスは、カスタム デバイス ID レジストリ/認証スキームで認証し、トークン サービスがトークンの作成に使用するデバイス ID を特定できます。
3. トークン サービスは、トークンを返します。トークンは、[IoT Hub 開発者ガイドのセキュリティ セクション][lnk-devguide-security]に従って、`/devices/{deviceId}` を `resourceURI` として使用して作成されます。`deviceId` は、認証されたデバイスです。トークン サービスは、共有アクセス ポリシーを使用してトークンを作成します。
4. デバイスは、IoT Hub で直接トークンを使用します。

> [AZURE.NOTE] .NET クラス [SharedAccessSignatureBuilder][lnk-dotnet-sas] または Java クラス [IotHubServiceSasToken][lnk-java-sas] を使用して、トークン サービスでトークンを作成できます。

トークン サービスは、必要に応じて、トークンの有効期限を設定できます。トークンの期限が切れた時点で、IoT Hub はデバイスの接続を切断します。その後、デバイスは新しいトークンをトークン サービスに要求する必要があります。有効期限までの期間を短くすると、デバイスとトークン サービスの両方に対する負荷が増加します。

デバイスをハブに接続するには、デバイスが接続にデバイス キーではなくトークンを使用している場合でも、引き続き IoT Hub のデバイス ID レジストリにデバイスを追加する必要があります。そのため、デバイスでトークンを使った認証を行うときに、[IoT Hub ID レジストリ][lnk-devguide-identityregistry]でデバイス ID を有効または無効にすることで、引き続きデバイスごとのアクセス制御を使用できます。これにより、有効期限までの期間が長い場合にトークンを使用するリスクが軽減されます。

### カスタム ゲートウェイとの比較

IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。これにより、IoT Hub がほとんどのソリューション トラフィックを処理できるためです。ただし、カスタム認証スキームとプロトコルとの関係が複雑すぎるため、すべてのトラフィックを処理するサービス (*カスタム ゲートウェイ*) が必要になる場合があります。その例として、[トランスポート層セキュリティ (TLS) と事前共有キー (PSK)][lnk-tls-psk] があります。詳細については、[プロトコル ゲートウェイ][lnk-gateway]に関する記事を参照してください。

## デバイスのハートビート <a id="heartbeat"></a>

[IoT Hub ID レジストリ][lnk-devguide-identityregistry]には、**connectionState** というフィールドが含まれています。**connectionState** フィールドの使用は、開発およびデバッグ中の使用にとどめ、IoT ソリューションでは実行時にこのフィールドを照会しないでください (たとえば、C2D メッセージまたは SMS を送信するかどうかを決定する場合にデバイスが接続されているかどうかを確認する目的で)。デバイスが接続されているかどうかを、IoT ソリューションで把握する必要がある場合 (実行時に、または **connectionState** プロパティでの指定よりも正確に)、ソリューションでは*ハートビート パターン*を実装する必要があります。

ハートビート パターンでは、デバイスは一定の時間ごとに 1 回以上、D2C メッセージを送信します。(たとえば、1 時間ごとに 1 回以上)。そうした場合、デバイスは送信するデータを保持していなくても、空の D2C メッセージを送信します (通常は、それをハートビートとして識別するプロパティを伴って)。サービス側でソリューションは、デバイスごとに受信した最後のハートビートが示されたマップを保持し、所定の時間内にハートビート メッセージを受信しなかった場合はデバイスに問題があると見なします。

より複雑な実装になると、[操作の監視][lnk-devguide-opmon]からの情報を取り込むことで、接続または通信を試みているが失敗しているデバイスを識別することが可能です。ハートビート パターンを実装する場合は、[IoT Hub のクォータとスロットル][]に関するページを必ず確認してください。

## 次のステップ

Azure IoT Hub についてさらに学習するには、次のリンクを使用してください。

- [IoT Hub の使用 (チュートリアル)][lnk-get-started]
- [What is Azure IoT Hub? (Azure IoT Hub とは)][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[IoT Hub のクォータとスロットル]: iot-hub-devguide.md#throttling

<!---HONumber=AcomDC_0204_2016-->