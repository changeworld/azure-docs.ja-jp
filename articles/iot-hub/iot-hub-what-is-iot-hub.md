<properties
 pageTitle="Azure IoT Hub の概要 | Microsoft Azure"
 description="Azure IoT Hub サービスの概要: IoT Hub、デバイスの接続、モノのインターネット (IoT) の通信パターン、サービス支援通信パターンの説明"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# Azure IoT Hub とは

Azure IoT Hub へようこそ。この記事では、Azure IoT Hub の概要と、モノのインターネット (IoT) ソリューションを実装したときに、このサービスを使用する理由について説明します。Azure IoT Hub は、何百万もの IoT デバイスとソリューション バックエンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。Azure IoT Hub の特長は次のとおりです。

- デバイスとクラウド間の信頼性の高い双方向メッセージングを大規模に提供します。
- デバイスごとのセキュリティ資格情報とアクセス制御を使用して、セキュリティで保護された通信を可能します。
- デバイス接続イベントおよびデバイス ID 管理イベントの詳細な監視を実現します。
- 最も一般的な言語とプラットフォームのデバイスのライブラリが含まれます。

「[IoT Hub と Event Hubs の比較][lnk-compare]」では、この 2 つのサービスの主な違いについて説明し、IoT ソリューションで IoT Hub を使用する利点を示しています。

![Azure IoT Hub as cloud gateway in internet of things solution][img-architecture]

> [AZURE.NOTE] IoT アーキテクチャの詳細については、「[Microsoft Azure IoT Reference Architecture (Microsoft Azure IoT リファレンス アーキテクチャ)][lnk-refarch]」を参照してください。

## IoT デバイスの接続の課題

IoT Hub とデバイスのライブラリでは、デバイスをソリューション バック エンドに安定して安全に接続するための課題に対処できます。IoT デバイスの特性は次のとおりです。

- 多くの場合、人間が操作することのない組み込みシステムです。
- 物理アクセスにコストがかかる遠隔地に配置されている場合があります。
- ソリューション バック エンド経由でしか到達できない場合があります。
- 電源や処理リソースが限られている場合があります。
- ネットワーク接続が断続的に切れたり、遅かったり、高コストである場合があります。
- 専用、カスタム、または業界固有のアプリケーション プロトコルを使用することが必要になる場合があります。
- 一般的なハードウェアおよびソフトウェア プラットフォームを多数使用して作成できます。

上記の要件に加え、IoT ソリューションはスケール、セキュリティ、および信頼性を提供する必要もあります。そのため、一連の接続要件は、Web コンテナーやメッセージング ブローカーなどの従来のテクノロジを使用すると、実装するのが難しく、時間がかかります。

## Azure IoT Hub を使用する理由

Azure IoT Hub は、次の方法でデバイスの接続の課題に対処します。

-   **デバイスごとの認証およびセキュリティで保護された接続**。各デバイスを独自の[セキュリティ キー][lnk-devguide-security]を使用してプロビジョニングし、IoT Hub に接続することができます。[IoT Hub ID レジストリ][lnk-devguide-identityregistry]には、ソリューションのデバイスの ID とキーが保存されます。ソリューション バックエンドで個々のデバイスを許可リストと拒否リストのいずれかに追加し、デバイスのアクセスを完全に制御できます。

-   **デバイスの接続操作の監視**。デバイス ID の管理操作とデバイス接続イベントに関する詳細な操作ログを受信できます。この監視機能により、IoT ソリューションでは、接続の問題 (不適切な資格情報で接続を試みるデバイス、メッセージを送信する頻度が異常であるデバイス、すべての C2D メッセージを拒否するデバイスなど) を識別することができます。

-   **広範なデバイス ライブラリ**。[Azure IoT デバイス SDK][lnk-device-sdks] は、さまざまな言語とプラットフォーム (多くの Linux ディストリビューション、Windows、リアルタイム オペレーティング システムでは C) で使用でき、サポートされています。Azure IoT デバイス SDK はまた、C#、Java、JavaScript など管理対象言語に対応しています。

-   **IoT プロトコルと機能拡張**。ソリューションでデバイス ライブラリを使用できない場合、IoT Hub は、デバイスで MQTT v3.1.1、HTTP 1.1、または AMQP 1.0 プロトコルをネイティブに使用できるようにするパブリック プロトコルを公開します。また、以下の方法で IoT Hub を拡張して、カスタム プロトコルをサポートすることもできます。

    - IoT Hub で認識される 3 つのプロトコルのいずれかにカスタム プロトコルを変換するフィールド ゲートウェイを、[Azure IoT Gateway SDK][lnk-gateway-sdk] で作成します。
    - クラウドで実行されるオープン ソース コンポーネントである [Azure IoT プロトコル ゲートウェイ][protocol-gateway]をカスタマイズします。

-   **スケール**。Azure IoT Hub は、デバイスの数百万単位での同時接続、および毎秒数百万単位のイベントに対応できます。

多くの通信パターンにこれらの利点があります。現在 IoT Hub では、具体的に次の通信パターンを実装できます。

-   **イベント ベースのデバイスからクラウドへの取り込み。** IoT Hub は、デバイスから 1 秒ごとに何百万ものイベントを安定して受信できます。その後、イベント プロセッサ エンジンを使用してホット パスでイベントを処理できます。また、分析のためにコールド パスに保存することもできます。IoT Hub は、信頼性の高い処理を保証するためとピーク時の負荷を吸収するために、最大 7 日間イベント データを保持します。

-   **クラウドからデバイスへの信頼性の高いメッセージング (または*コマンド*)。** ソリューション バック エンドでは、IoT Hub を使用して、最低 1 回の配信保証で個々のデバイスにメッセージを送信できます。各メッセージには個別の有効期限設定があり、バックエンドでは配信と有効期限の両方の受信通知を要求し、これらの受信通知により、クラウドからデバイスへのメッセージのライフサイクルについて完全な可視性を保証します。これにより、デバイス上で実行される操作を含むビジネス ロジックの実装が可能になります。

-   **クラウドへのファイルとキャッシュされたセンサー データのアップロード。** IoT Hub によって自動的に管理される SAS URI を使用して、デバイスから Azure Storage にファイルをアップロードできます。IoT Hub は、クラウドにファイルが届いたときに通知を生成して、バックエンドでそのファイルを処理できるようにします。

## ゲートウェイ

通常、IoT ソリューションのゲートウェイは、クラウドにデプロイされる[プロトコル ゲートウェイ][lnk-gateway]か、デバイスでローカルにデプロイされる[フィールド ゲートウェイ][lnk-field-gateway]のいずれかになります。プロトコル ゲートウェイは、MQTT から AMQP への変換など、プロトコル変換を実行します。フィールド ゲートウェイは、現状分析を実行したり、時間の制約がある判断を行って遅延を短縮したりできます。また、デバイス管理サービスを提供したり、セキュリティとプライバシーの制約を強制したりできるほか、プロトコル変換を行うこともできます。どちらの種類のゲートウェイも、デバイスと IoT Hub 間の仲介役として機能します。

フィールド ゲートウェイは通常、ソリューションにおけるアクセスと情報フローの管理において大きな役割を果たすため、単純なトラフィック ルーティング デバイス (ネットワーク アドレス変換デバイスやファイアウォールなど) とは異なります。

プロトコル ゲートウェイとフィールド ゲートウェイの両方をソリューションに含めることもできます。

## IoT Hub のしくみ

Azure IoT Hub は、デバイスとソリューション バックエンド間の対話を仲介する[サービス支援通信][lnk-service-assisted-pattern]パターンを実装しています。サービス支援通信の目的は、管理システム (IoT Hub など) と、信頼されていない物理領域にデプロイされている特殊用途のデバイス間の信頼できる双方向の通信パスを確立することです。このパターンでは次の原則が確立されています。

- セキュリティがその他のすべての機能に優先されます。
- デバイスは、要求されていないネットワーク情報を受け入れません。デバイスは送信のみの方法で、すべての接続とルートを確立します。バック エンドからコマンドを受信する場合、デバイスは定期的に接続を開始して、処理待ちのコマンドがないか確認する必要があります。
- デバイスは、IoT Hub など、ピアリングされている既知のサービスへの接続またはルートの確立のみを行います。
- デバイスとサービス間またはデバイスとゲートウェイ間の通信パスは、アプリケーション プロトコル レイヤーで保護されます。
- システム レベルの承認と認証は、デバイスごとの ID に基づいており、アクセス資格情報とアクセス許可をほぼ瞬時に取り消すことができます。
- コマンドとデバイスの通知は、デバイスが接続され、それらを受信するまで保持されるので、電源や接続の問題によって散発的に接続されるデバイスの双方向通信が円滑になります。IoT Hub は、送信されるコマンドのデバイス固有のキューを維持しています。
- アプリケーション ペイロード データは個別に保護され、ゲートウェイを介して特定のサービスに転送されます。

モバイル業界では、サービス支援通信パターンを大規模に使用して、[Windows Push 通知サービス][lnk-wns]、[Google Cloud Messaging][lnk-google-messaging]、[Apple Push Notification サービス][lnk-apple-push]などのプッシュ通知サービスを実装しています。

## 次のステップ

Azure IoT Hub では、標準ベースの IoT デバイス管理が可能となっており、リモートからデバイスを管理、構成、更新することができます。詳細については、「[Azure IoT Hub デバイス管理の概要][lnk-device-management]」を参照してください。

クライアント アプリケーションを各種デバイス ハードウェア プラットフォームやオペレーティング システムに実装するために、IoT デバイス SDK を使用できます。IoT デバイス SDK には、テレメトリを IoT Hub に送信し、クラウドからデバイスへのコマンドを受信する操作を容易にするライブラリが含まれています。これらの SDK を使用すると、さまざまなネットワーク プロトコルのうちのいずれかを選択して IoT Hub と通信することができます。詳細については、[デバイス SDK に関する情報][lnk-device-sdks]を参照してください。

実際にコードを作成してサンプルを実行するには、[IoT Hub の使用][lnk-get-started]に関するチュートリアルを参照してください。

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "サービス支援通信に関する Clemens Vasters によるブログの投稿"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_1005_2016-->