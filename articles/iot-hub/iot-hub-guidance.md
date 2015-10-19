<properties
 pageTitle="Azure IoT Hub に関するガイダンス | Microsoft Azure"
 description="Azure IoT Hub を使用したソリューションに関するガイダンスのトピックのコレクション。"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Azure IoT Hub ガイダンス

## デバイス プロビジョニング

IoT ソリューションはさまざまなシステムとストアのデバイス情報を保持します。アプリケーション固有のデバイス情報を保持するストアの 1 つとして [IoT Hub の IT レジストリ][IoT Hub Developer Guide - identity registry]があります。*プロビジョニング*とは、こうしたシステムすべてに必要なデバイス情報を作成するプロセスです。

デバイス プロビジョニングの要件と戦略は多数あります (詳細については、[IoT Hub デバイス管理のガイダンス]を参照してください)。[IoT Hub ID レジストリ][IoT Hub Developer Guide - identity registry]には、プロビジョニング プロセスで IoT Hub を統合するときに必要な API が用意されています。

## フィールド ゲートウェイ

フィールド ゲートウェイは、特別なデバイス アプライアンスまたは汎用的なソフトウェアで、通信イネーブラーとして、また、場合によってはローカルのデバイス管理システムおよびデバイスのデータ処理ハブとして動作します。フィールド ゲートウェイは、デバイスに対するローカル処理および制御機能を実行しながら、デバイスのテレメトリをフィルター処理または集計し、バックエンドに送信されるデータ量を減らすことができます。

フィールド ゲートウェイのスコープには、フィールド ゲートウェイ自体と自身に接続されているすべてのデバイスが含まれます。フィールド ゲートウェイは、その名前が示すように専用データ処理施設外で動作し、通常は、場所がバインドされています。また、アクセスと情報フローを管理するうえで大きな役割を担っているという点で、単なるトラフィック ルーターとは異なります。つまり、これはアプリケーション対応のエンティティで、ネットワーク接続またはセッション ターミナルです (たとえば、この場合、ゲートウェイはデバイス プロビジョニング、データ変換、プロトコル変換、およびイベント ルールの処理に役立ちます)。これに対し、NAT デバイスまたはファイアウォールはフィールド ゲートウェイとは見なされません。これは明示的な接続またはセッション ターミナルではなく、これらを介して行われるルート (または拒否) 接続またはセッションであるためです。

### 透過的なフィールド ゲートウェイと非透過的なフィールド ゲートウェイ

デバイス ID に関しては、フィールド ゲートウェイのスコープ内にある他のデバイスの ID が IoT Hub の ID レジストリに含まれる場合、そのフィールド ゲートウェイは*透過的*です。IoT Hub の ID レジストリに含まれるのが、フィールド ゲートウェイの ID のみである場合、そのフィールド ゲートウェイは*非透過的*です。

重要なのは、*非透過的*なゲートウェイでは、IoT Hub が[デバイス ID なりすまし対策][IoT Hub Developer Guide - Anti-spoofing]を提供できない点です。また、フィールド ゲートウェイのスコープ内にあるすべてのデバイスが IoT Hub では 1 つのデバイスとして示されるため、1 つのデバイスとしてスロットルおよびクォータが適用されます。

### その他の考慮事項

フィールド ゲートウェイは、[Azure IoT デバイス SDK][] を使って実装できます。一部の SDK には、IoT Hub への 1 つの接続で複数のデバイス通信を多重化する機能など、フィールド ゲートウェイ固有の機能が用意されています。[IoT Hub 開発者ガイド: 通信プロトコルの選択][]に関するページで説明されているように、フィールド ゲートウェイに対してはトランスポート プロトコルとして HTTP/1 を使用しないでください。

## カスタム デバイスの認証のスキームとサービスの使用

IoT Hub では、[デバイス ID レジストリ][IoT Hub Developer Guide - identity registry]を使って、デバイスごとのセキュリティ資格情報とアクセス制御を構成できます。

既にカスタム デバイス ID レジストリや認証スキームにかなり投資している IoT ソリューションで、IoT Hub の他の機能を引き続き利用するには、IoT Hub の*トークン サービス*を作成します。

トークン サービスは、自己デプロイされたクラウド サービスです。このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub *共有アクセス ポリシー*を使用して、*デバイスを対象とする*トークンを作成します。

  ![][img-tokenservice]

トークン サービス パターンの主な手順を次に示します。

1. [IoT Hub 共有アクセス ポリシー][IoT Hub Developer Guide - Security]を作成し、IoT Hub に対する **DeviceConnect** アクセス許可を指定します。トークン サービスは、このポリシーを使用してトークンに署名します。
2. IoT Hub にアクセスする必要があるデバイスは、トークン サービスに署名付きトークンを要求します。デバイスは、カスタム デバイスの ID レジストリと認証スキームを使用できます。
3. トークン サービスは、[IoT Hub 開発者ガイド: セキュリティ][]に関するページに従って作成されたトークンを、`/devices/{deviceId}` を `resourceURI` として使用して返します。`deviceId` は、認証されたデバイスです。
4. デバイスは、IoT Hub で直接トークンを使用します。

トークン サービスは、必要に応じて、トークンの有効期限を設定できます。期限が切れた時点で、IoT Hub は接続を提供するため、デバイスは、新しいトークンをトークン サービスに要求する必要があります。有効期限までの期間を短くすると、デバイスとトークン サービスの両方に対する負荷が増加するのは明らかです。

デバイスが接続できるように、IoT Hub でデバイス ID を引き続き作成するように指定してもよいでしょう。それにより、デバイスでトークンを使った認証を行う場合でも、[IoT Hub 開発者ガイド: ID レジストリ][]に関するページの手順に従ってデバイス ID を無効にすることで、デバイスごとのアクセス制御も引き続き有効になります。これにより、長期間有効なトークンが少なくなります。

### カスタム ゲートウェイとの比較

IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。これにより、IoT Hub がほとんどのソリューション トラフィックを処理できるためです。ただし、カスタム認証スキームとプロトコルとの関係が複雑すぎるため ([TLS PSK][] など)、すべてのトラフィックを処理するサービス (*カスタム ゲートウェイ*) が必要になる場合があります。詳細については、[プロトコル ゲートウェイ][]に関する記事を参照してください。

## 次のステップ

Azure IoT Hub についてさらに学習するには、次のリンクを使用してください。

- [IoT Hubs の使用 (チュートリアル)][lnk-get-started]
- [What is Azure IoT Hub? (Azure IoT Hub とは)][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[IoT Hub 開発者ガイド: ID レジストリ]: iot-hub-devguide.md#identityregistry
[IoT Hub デバイス管理のガイダンス]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Azure IoT デバイス SDK]: iot-hub-sdks-summary.md
[IoT Hub 開発者ガイド: 通信プロトコルの選択]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[IoT Hub 開発者ガイド: セキュリティ]: iot-hub-devguide.md#security
[TLS PSK]: https://tools.ietf.org/html/rfc4279
[プロトコル ゲートウェイ]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub? (Azure IoT Hub とは)]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO2-->