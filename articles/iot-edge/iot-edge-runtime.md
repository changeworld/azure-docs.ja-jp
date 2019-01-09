---
title: ランタイムでデバイスを管理する方法について - Azure IoT Edge | Microsoft Docs
description: デバイス上のモジュール、セキュリティ、通信、およびレポートを Azure IoT Edge ランタイムで管理する方法について説明します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3495d157f1a681e80b6d113acced53d01751690f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077496"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge ランタイムとそのアーキテクチャの概要

IoT Edge ランタイムは、プログラムの集合体で、IoT Edge デバイスと認識されるためには、デバイスにインストールする必要があります。 これらの IoT Edge ランタイムで構成されるコンポーネントを使用することにより、IoT Edge デバイスは、エッジで実行するコードを受信し、結果を通信できます。 

IoT Edge ランタイムは、IoT Edge デバイスで次の機能を実行します。

* デバイスにワークロードをインストールし、更新する。
* デバイス上の Azure IoT Edge のセキュリティ標準を維持する。
* [IoT Edge モジュール](iot-edge-modules.md)の実行状態を絶えず確保する。
* モジュールの正常性をクラウドに報告してリモート監視を可能にする。
* ダウンストリームのリーフ デバイスと IoT Edge デバイス間の通信を円滑化する。
* IoT Edge デバイス上のモジュール間の通信を円滑化する。
* IoT Edge デバイスとクラウドとの間の通信を円滑化する。

![ランタイムによる分析情報とモジュールの正常性の IoT Hub への通信](./media/iot-edge-runtime/Pipeline.png)

IoT Edge ランタイムには、通信とモジュール管理の 2 つのカテゴリの役割があります。 これら 2 つの役割は、IoT Edge ランタイムを構成する 2 つのコンポーネントによって実行されます。 IoT Edge ハブは通信を実行し、IoT Edge エージェントは、モジュールの展開と監視を実行します。 

Edge ハブと Edge エージェントは、いずれも、IoT Edge デバイス上で実行される他のモジュールと同様のモジュールです。 

## <a name="iot-edge-hub"></a>IoT Edge ハブ

Edge ハブでは、Azure IoT Edge ランタイムを構成する 2 つのモジュールの 1 つです。 Edge ハブは、IoT Hub と同じプロトコル エンドポイントを公開することで IoT Hub のためのローカル プロキシとして動作します。 この整合性により、クライアント (デバイスまたはモジュール) は、IoT Hub と同じように IoT Edge ランタイムに接続できます。 

>[!NOTE]
>Edge ハブでは、MQTT または AMQP を使用して接続するクライアントがサポートされます。 HTTP を使用するクライアントはサポートされません。 

Edge ハブは、ローカルで実行される完全バージョンの IoT Hub ではありません。 Edge ハブは、いくつかの機能を IoT Hub をサイレントにデリゲートします。 たとえば、Edge ハブは、デバイスがはじめて接続を試みたとき、IoT Hub に認証要求を送信します。 初めて接続を確立した後は、セキュリティ情報は Edge ハブによってローカルでキャッシュされます。 そのデバイスからのその後の接続は、クラウドへの認証なしに許可されます。 

>[!NOTE]
>デバイスの認証を試みるたびに、ランタイムの接続が必要です。

IoT Edge ソリューションが使用する帯域幅を減らすために、Edge ハブは、クラウドへの実際の接続数を最適化します。 Edge ハブは、モジュールまたはリーフ デバイスなどのクライアントからの論理接続を取得し、クラウドへの 1 つの物理接続に統合します。 ソリューションの他の部分は、このプロセスの詳細を認識する必要がありません。 クライアントは、すべて、共通の接続を使って接続しているにもかかわらず、クラウドにそれぞれ独自に接続していると認識します。 

![Edge ハブは物理デバイスと IoT Hub との間のゲートウェイです。](./media/iot-edge-runtime/Gateway.png)

Edge ハブでは、IoT Hub に接続されているかどうかを判断できます。 接続が切断された場合は、Edge ハブがメッセージを保存するか、ツインがローカルで更新します。 接続が再確立されると、すべてのデータが同期されます。 この一時キャッシュに使用される場所は、Edge ハブのモジュール ツインのプロパティによって規定されます。 キャッシュのサイズには上限がなく、デバイスにストレージ容量がある限り拡張されます。 

### <a name="module-communication"></a>モジュール通信

Edge Hub を使用することで、モジュール間の通信が容易になります。 メッセージ ブローカーとして Edge Hub を使用することで、モジュールの相互独立性を維持できます。 モジュールは、メッセージを受信する入力と、メッセージを書き出す出力を指定するだけです。 ソリューション開発者は、そのソリューションに固有の順序でデータを処理できるよう、これらの入力と出力を統合します。 

![Edge Hub を使用することで、モジュール間の通信が容易になります。](./media/iot-edge-runtime/module-endpoints.png)

データを Edge ハブに送信するために、モジュールは、SendEventAsync メソッドを呼び出します。 最初の引数は、どの出力にメッセージを送信するかを指定します。 次の疑似コードは、output1 にメッセージを送信します。

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

メッセージを受信するには、特定の入力で受け取ったメッセージを処理するコールバックを登録します。 次の疑似コードは、input1 で受信したすべてのメッセージを処理する関数 messageProcessor を登録します。

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

ModuleClient クラスとその通信方法に関する詳細については、優先する SDK 言語 ([C# ](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、[C および Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)、[Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)) の API リファレンスを参照してください。

ソリューション開発者は、Edge ハブがモジュール間でメッセージを渡す方法を決定するルールを指定します。 ルーティング ルールはクラウドで定義され、そのデバイス ツインで Edge ハブにプッシュ ダウンされます。 IoT Hub ルートと同じ構文を使用して、Azure IoT Edge のモジュール間のルートが定義されます。 

<!--- For more info on how to declare routes between modules, see []. --->   

![モジュール間のルートは Edge ハブを経由して移動します](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge エージェント

IoT Edge エージェントは、Azure IoT Edge ランタイムを構成するもう 1 つのモジュールです。 このモジュールは、モジュールをインスタンス化し、モジュールの実行を継続し、IoT Hub にモジュールのステータスを報告します。 Edge エージェントは、他のモジュールと同じように、そのモジュール ツインを使用して、この構成データを格納します。 

[IoT Edge セキュリティ デーモン](iot-edge-security-manager.md)がデバイスの起動時に Edge エージェントを開始します。 エージェントは IoT Hub からそのモジュール ツインを取得し、配置マニフェストを検査します。 配置マニフェストとは、開始する必要があるモジュールを宣言する JSON ファイルです。 

配置マニフェスト内の各項目にはモジュールに関する特定の情報が含まれており、モジュールのライフ サイクルを制御するために Edge エージェントによって使用されます。 重要なプロパティを次に示します。 

* **settings.image** – Edge エージェントがモジュールを起動するために使用するコンテナー イメージ。 イメージがパスワードで保護されている場合は、コンテナー レジストリの資格情報を Edge エージェントに設定する必要があります。 コンテナー レジストリの資格情報は、配置マニフェストを使用してリモートで構成するか、IoT Edge のプログラム フォルダー内の `config.yaml` ファイルを更新することで、Edge デバイス自体で構成できます。
* **settings.createOptions** – モジュールのコンテナーを起動したときに、Docker デーモンに直接渡される文字列。 このプロパティで Docker オプションを追加することにより、ポート転送またはモジュール コンテナーへのボリュームのマウントなどの詳細オプションを設定できます。  
* **status** – Edge エージェントがモジュールに設定する状態。 大部分のユーザーが Edge を使ってデバイス上のすべてのモジュールをただちに起動する必要があるため、この値は、通常、*実行中*に設定されます。 また、モジュールの初期状態を停止中に指定し、後で Edge エージェントを起動するよう指定することができます。 Edge エージェントは、報告されたプロパティで、クラウドに各モジュールの状態を報告します。 期待されるプロパティと報告されたプロパティの間に差がある場合は、デバイスの動作が不適切であることを示しています。 次の状態がサポートされています。
   * ダウンロード中
   * 実行中
   * 異常
   * 失敗
   * 停止済み
* **restartPolicy** – Edge エージェントが、モジュールを再起動する方法です。 指定できる値は、次のとおりです。
   * Never – Edge エージェントが、モジュールを再起動することはありません。
   * onFailure - モジュールがクラッシュした場合、Edge エージェントがモジュールを再起動します。 モジュールがクリーンにシャット ダウンした場合、Edge エージェントはモジュールを再起動しません。
   * Unhealthy - モジュールがクラッシュしたか、異常と判断された場合は、Edge エージェントによって再起動されます。
   * Always - モジュールがクラッシュしたか、異常と判断された場合、あるいは方法に関係なくシャットダウンされた場合は、Edge エージェントによって再起動されます。 

IoT Edge エージェントは、ランタイム応答を IoT ハブに送信します。 考えられる応答の一覧を以下に示します。
  * 200 - OK
  * 400 - デプロイ構成が正しくない形式であるか、無効です。
  * 417 - デバイスにデプロイ構成セットがありません。
  * 412 - デプロイ構成にあるスキーマ バージョンが無効です。
  * 406 - エッジ デバイスがオフラインであるか、状態レポートを送信していません。
  * 500 - エッジ ランタイムでエラーが発生しました。

### <a name="security"></a>セキュリティ

IoT Edge エージェントは、IoT Edge デバイスのセキュリティ上、重要な役割を果たします。 たとえば、起動前のモジュール イメージの検証などの操作を実行します。 

Azure IoT Edge セキュリティ フレームワークについて詳しくは、[IoT Edge セキュリティ マネージャー](iot-edge-security-manager.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure IoT Edge の証明書について](iot-edge-certs.md)
