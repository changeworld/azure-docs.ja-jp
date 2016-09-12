<properties
 pageTitle="IoT Hub に関するガイダンス | Microsoft Azure"
 description="Azure IoT Hub を使用して IoT ソリューションを開発するためのゲートウェイ、デバイス プロビジョニング、および認証に関するガイダンス トピックです。"
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
 ms.date="07/19/2016"
 ms.author="dobett"/>

# ソリューションの設計

この記事では、モノのインターネット (IoT) ソリューションで次の機能を設計する方法について説明します。

- デバイス プロビジョニング
- フィールド ゲートウェイ
- デバイスの認証
- デバイスのハートビート

## デバイス プロビジョニング

IoT ソリューションでは、次のような個々のデバイスに関するデータを格納します。

- デバイスの ID と認証キー
- デバイス ハードウェアの種類とバージョン
- デバイスの状態
- ソフトウェアのバージョンと機能
- デバイス コマンドの履歴

特定の IoT ソリューションに格納されるデバイス データは、そのソリューションの具体的な要件によって異なりますが、ソリューションには少なくともデバイスの ID と認証キーを格納する必要があります。Azure IoT Hub には、ID、認証キー、状態コードなど、各デバイスの値を格納できる [ID レジストリ][lnk-devguide-identityregistry]があります。ソリューションでは、テーブル、BLOB、Azure DocumentDB など、その他の Azure サービスを使用して、追加のデバイス データを格納できます。

*デバイス プロビジョニング*とは、最初のデバイス データをソリューション内のストアに追加するプロセスです。新しいデバイスをハブに接続できるようにするには、新しいデバイスの ID とキーを [IoT Hub ID レジストリ][lnk-devguide-identityregistry]に追加する必要があります。プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。

[IoT Hub ID レジストリ API][lnk-devguide-identityregistry] を使用すると、IoT Hub をプロビジョニング プロセスに統合できます。

## フィールド ゲートウェイ

IoT ソリューションでは、*フィールド ゲートウェイ*はデバイスと IoT Hub の間に配置され、通常はデバイスの近くにあります。デバイスは、そのデバイスでサポートされているプロトコルを使用してフィールド ゲートウェイと直接通信します。フィールド ゲートウェイは、IoT Hub でサポートされているプロトコルを使用して IoT Hub と通信します。フィールド ゲートウェイとして使用できるのは、非常に特殊なハードウェアまたは低電力コンピューターであり、そこで実行されているソフトウェアによって、ゲートウェイが対象としているエンド ツー エンド シナリオが実現します。

フィールド ゲートウェイは、通常、ソリューションにおけるアクセスと情報フローの管理において大きな役割を果たすため、単純なトラフィック ルーティング デバイス (ネットワーク アドレス変換 (NAT) デバイスやファイアウォールなど) とは異なります。たとえば、フィールド ゲートウェイでは次のことができます。

- **新旧デバイスのサポートの追加**: 従来のセンサーやアクチュエータだけでなく、新しいデバイスの中にも、データをクラウドに直接送信できないものは多数あります。こうしたデバイスで採用されているプロトコルはインターネットに適しておらず、暗号化が実装されていません。または、デバイスに ID 証明書を格納できません。ゲートウェイを使用すると、これらのデバイスの接続負荷とコストが削減されます。
- **エッジ分析の実行**: ローカルで実行できる処理が多くあり、クラウドとやり取りするデータの量を減らすことができます。たとえば、データのフィルター処理、バッチ処理、圧縮をローカルで実行できます。また、オンプレミスのリアルタイム データによる機械学習モデルのスコア付け、データ クレンジングなどの計算を実行するときに、役に立つこともあります。
- **待機時間の最小化**: 製造ライン停止の防止や電力サービスの復元を目指している場合は、ミリ秒の違いが重要になってきます。データを収集したデバイスの近くでデータ分析を実行することが、障害回避とシステム エラー連鎖の明暗を分ける場合があります。
- **ネットワーク帯域幅の節約**: 一般的な海上石油プラットフォームで 1 日に生成されるデータ量は 1 ～ 2 TB です。ボーイング 787 ではフライトあたり 0.5 TB のデータが作成されます。数千台まやは数十万台のエッジ デバイスから大量のデータをクラウドに転送するのは現実的ではありません。また、不要でもあります。重要な分析の多くが、クラウド規模の処理およびストレージを必要としないためです。
- **確実な動作**: 市民の安全と重要なインフラストラクチャに影響を及ぼす決定に IoT データを使う機会はますます増えています。クラウド接続が断続的に切断されることで、インフラストラクチャとデータの整合性と可用性を損ねることがあってはいけません。格納、転送などの機能を使用して、データをローカルで収集し、作業を行い、必要に応じてクラウドに送信することで、信頼できるソリューションを作成できます。
- **プライバシーとセキュリティの問題への対処**: IoT デバイスと、そのデバイスで生成されたデータを保護する必要があります。ゲートウェイには、公開インターネットからデバイスを分離する、デバイス自体に暗号化サービス、ID サービスなどが用意されていない場合にこうしたサービスを提供する、ローカルにバッファーまたは格納されたデータを保護する、個人を特定できる情報を削除してからインターネット経由で送信する、などのサービスが用意されています。

### その他の考慮事項

[Azure IoT Gateway SDK][lnk-gateway-sdk] を使用してフィールド ゲートウェイを実装できます。この SDK には、同じ接続経由での複数のデバイスから IoT Hub への通信を多重化する機能など、固有の機能が用意されています。

## カスタム デバイスの認証

IoT Hub の[デバイス ID レジストリ][lnk-devguide-identityregistry]を使用して、デバイスごとのセキュリティ資格情報とアクセス制御を[トークン][lnk-sas-token]により構成できます。ただし、IoT ソリューションで既にカスタム デバイス ID レジストリや認証スキームにかなり投資している場合、*トークン サービス*を作成すると、この既存のインフラストラクチャを IoT Hub と統合できます。この方法により、ソリューションで他の IoT 機能を使用できます。

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

IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。これにより、IoT Hub がほとんどのソリューション トラフィックを処理できるためです。ただし、カスタム認証スキームとプロトコルとの関係が複雑すぎるため、すべてのトラフィックを処理するサービス (*カスタム ゲートウェイ*) が必要になる場合があります。その例として、[トランスポート層セキュリティ (TLS) と事前共有キー (PSK)][lnk-tls-psk] があります。詳細については、[プロトコル ゲートウェイ][lnk-protocols]に関するトピックをご覧ください。

## デバイスのハートビート<a id="heartbeat"></a>

[IoT Hub ID レジストリ][lnk-devguide-identityregistry]には、**connectionState** というフィールドが含まれています。**connectionState** フィールドの使用は開発およびデバッグ中の使用にとどめ、IoT ソリューションでは実行時にこのフィールドを照会しないでください (たとえば、C2D メッセージまたは SMS を送信するかどうかを決定する場合にデバイスが接続されているかどうかを確認する目的で)。デバイスが接続されているかどうかを (実行時に、または **connectionState** プロパティを使用するよりも正確に) IoT ソリューションで把握する必要がある場合は、ソリューションに*ハートビート パターン*を実装してください。

ハートビート パターンでは、デバイスは一定の時間ごとに 1 回以上、D2C メッセージを送信します。(たとえば、1 時間ごとに 1 回以上)。そうした場合、デバイスは送信するデータを保持していなくても、空の D2C メッセージを送信します (通常は、それをハートビートとして識別するプロパティを伴って)。サービス側でソリューションは、デバイスごとに受信した最後のハートビートが示されたマップを保持し、所定の時間内にハートビート メッセージを受信しなかった場合はデバイスに問題があると見なします。

より複雑な実装になると、[操作の監視][lnk-devguide-opmon]からの情報を取り込むことで、接続または通信を試みているが失敗しているデバイスを識別することが可能です。ハートビート パターンを実装する場合は、[IoT Hub のクォータとスロットル][]を必ず確認してください。

> [AZURE.NOTE] C2D メッセージを送信するかどうかを判断するためだけに IoT ソリューションでデバイス接続状態が必要であり、多数のデバイスにメッセージをブロードキャストしない場合は、もっと簡単なパターンとして短い有効期間の使用を検討する必要があります。この方法を使用すると、ハートビート パターンを使用してデバイス接続状態レジストリを維持するのと同じ結果を得られますが、効率は大幅に向上します。また、メッセージの受信確認を要求することにより、メッセージを受信できるデバイスおよびオンラインではないデバイスや障害が発生しているデバイスについて IoT Hub から通知を受け取ることもできます。C2D メッセージの詳細については、「[Azure IoT Hub 開発者ガイド][lnk-devguide-messaging]」をご覧ください。

## 次のステップ

IoT Hub のデプロイの計画に関する詳細については、以下をご覧ください。

- [MQTT サポート][lnk-mqtt]
- [サポートされているデバイス][lnk-devices]
- [その他のプロトコルのサポート][lnk-protocols]
- [Event Hubs との比較][lnk-compare]
- [HA と DR のスケーリング][lnk-scaling]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [開発者ガイド][lnk-devguide]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal-manage]
- [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279

[lnk-portal]: https://portal.azure.com
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[IoT Hub のクォータとスロットル]: iot-hub-devguide.md#throttling
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal-manage]: iot-hub-manage-through-portal.md
[lnk-sas-token]: iot-hub-sas-tokens.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0831_2016-->