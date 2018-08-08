---
title: Azure IoT Hub device SDK を利用して接続と信頼できるメッセージ処理を管理する
description: Azure IoT Hub device SDK を利用するとき、デバイスの接続とメッセージ処理を改善する方法について説明します。
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d321e82560f7676f77ed9161c1bce0304b7d5914
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397109"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Azure IoT Hub device SDK を利用して接続と信頼できるメッセージ処理を管理する

このガイドでは、Azure IoT device SDK の接続機能と信頼性の高いメッセージ処理機能を活用し、回復力のあるデバイス アプリケーションを開発する方法の概要を示します。 この記事の目的は、各種質問に答えることと次のシナリオに対応することです。

- 切断されたネットワーク接続を管理する
- 異なるネットワーク接続間の切り替えを管理する
- サービスの一時的な接続エラーに起因する再接続を管理する

実装の詳細は言語によって異なります。詳細については、リンクされている API ドキュメントか特定の SDK をご覧ください。

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>回復性の設計

IoT デバイスは、多くの場合、GSM や衛星など、非連続的で不安定なネットワーク接続に依存します。 さらに、クラウドベースのサービスとやりとりするとき、一時的なサービスの中断やインフラストラクチャレベルの障害など (一過性の障害と呼ばれています)、一時的な条件に起因してエラーが発生することがあります。 デバイス上で実行されているアプリケーションは、接続と再接続のメカニズムと、メッセージの送受信の再試行ロジックを管理する必要があります。 さらに、再試行方法の要件は、デバイスが関連する IoT シナリオと、デバイスのコンテキストと機能に大きく依存します。

Azure IoT Hub device SDK の目的は、Azure IoT Hub との間で接続を確立し、メッセージを送受信するための堅牢かつ包括的な方法を与えることでクラウドとデバイス間の接続と通信を簡素化することです。 開発者は既存の実装を変更し、特定のシナリオに合わせた再試行方針を開発することもできます。

以降のセクションでは、接続と信頼できるメッセージ処理をサポートする SDK 機能について取り上げます。

## <a name="connection-and-retry"></a>接続と再試行

このセクションでは、接続を管理するときに利用できる再接続と再試行のパターン、お使いのデバイス アプリケーションで異なる再試行方針を利用するための実装ガイド、デバイス SDK に関連する API についてまとめています。

### <a name="error-patterns"></a>エラー パターン
接続エラーはさまざまなレベルで発生します。

-  ソケットの切断や名前解決エラーなど、ネットワークのエラー
- リンクの解除やセッションの失効など、HTTP、AMQP、MQTT トランスポートのプロトコルレベル エラー
- 無効な資格情報など、ローカルの手違いか、クォータの超過やスロットリングなど、サービス動作に起因するアプリケーションレベル エラー

デバイス SDK は 3 つすべてのレベルでエラーを検出します。  OS 関連のエラーとハードウェア エラーは、デバイス SDK によって検出され、処理されます。  デザインは、Azure アーキテクチャ センターの[一時的な障害の処理に関するガイダンス](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)に基づいています。

### <a name="retry-patterns"></a>再試行パターン

接続エラーが検出されたときの再試行のプロセス全体は次のようになります。 
1. SDK がネットワーク、プロトコル、アプリケーションでエラーと関連エラーを検出します。
2. エラーの種類に基づき、SDK はエラー フィルターを利用し、再試行する必要があるかどうかを判断します。  SDK が**回復不可能なエラー**を特定した場合、操作 (接続と送受信) は停止され、SDK はユーザーに通知します。 回復不可能なエラーとは、認証エラーや不適切なエンドポイントによるエラーなど、回復できないことが SDK によって結論付けられたエラーです。
3. **回復不可能なエラー**が特定された場合、SDK は再試行ポリシーを利用して再試行を開始し、定義されている時間切れになるまで続けます。
4. 定義されている時間切れになると、SDK は接続や送信の再試行を停止し、ユーザーに通知します。
5.  SDK によって、ユーザーはコールバックをアタッチし、接続ステータス変更を受信できます。 

次の 3 つの再試行ポリシーが用意されています。
- **指数関数的後退とゆらぎ**: 既定の再試行ポリシーとしてこれが適用されます。  始めは集中的に再試行し、徐々に回数を減らし、最大遅延に達します (最大遅延を超えることはありません)。  このデザインは、[Azure アーキテクチャ センターの再試行に関するガイダンス](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)に基づいています。
- **カスタム再試行**: 選択した言語によっては、カスタム再試行ポリシーを実装し、それを RetryPolicy に挿入できます。 自分のシナリオが適している再試行ポリシーを設計できます。  これは C SDK では利用できません。
- **再試行なし**: 再試行ポリシーを "再試行なし" に設定するオプションがあります。再試行ロジックが無効になります。  接続が確立されていれば、SDK は一回の接続と一回のメッセージ送信を試行します。 このポリシーは一般的に、帯域幅やコストが懸念されるときに使用されます。   このオプションが選択されている場合、送信できなかったメッセージは失われ、回復できません。 

### <a name="retry-policy-apis"></a>再試行ポリシー API

   | SDK | SetRetryPolicy メソッド | ポリシー実装 | 実装ガイダンス |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **既定**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**カスタム:** 利用可能な [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) を利用<BR>**再試行なし:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS 実装](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **既定**: [ExponentialBackoffWithJitter クラス](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**カスタム:** [RetryPolicy インターフェイス](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)を実装<BR>**再試行なし:** [NoRetry クラス](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 実装](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **既定**: [ExponentialBackoff クラス](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**カスタム:** [IRetryPolicy インターフェイス](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)を実装<BR>**再試行なし:** [NoRetry クラス](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# 実装]() |
   | ノード| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **既定**: [ExponentialBackoffWithJitter クラス](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**カスタム:** [RetryPolicy インターフェイス](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)を実装<BR>**再試行なし:** [NoRetry クラス](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [ノード実装](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

以下は、このフローを示すコード サンプルです。 

#### <a name="net-implementation-guidance"></a>.NET 実装ガイダンス

下のコード サンプルでは、既定の再試行ポリシーを定義し、設定する方法を示しています。

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

CPU 使用率が高くなることを回避するために、次の再試行までの最小時間が 1 秒になるように、(ネットワークがないときや宛先までの経路がないときなど) コードが直後に失敗した場合は再試行が調整されます。 

サービスから調整エラーが返された場合、再試行ポリシーが違っており、パブリック API からは変更できません。

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

再試行メカニズムは `DefaultOperationTimeoutInMilliseconds` 後に停止しますが、これは現在、4 分に設定されています。

#### <a name="other-languages-implementation-guidance"></a>他の言語の実装ガイダンス
他の言語については、下の実装ドキュメントをご覧ください。  再試行ポリシー API の使用方法を示したサンプルがリポジトリにあります。
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>次の手順
- [デバイス SDK とサービス SDK の使用](.\iot-hub-devguide-sdks.md)
- [C 用 Azure IoT device SDK の使用](.\iot-hub-device-sdk-c-intro.md)
- [制約のあるデバイスの場合の開発](.\iot-hub-devguide-develop-for-constrained-devices.md)
- [モバイル デバイスの場合の開発](.\iot-hub-how-to-develop-for-mobile-devices.md)
- [デバイスの切断のトラブルシューティング](iot-hub-troubleshoot-connectivity.md)
