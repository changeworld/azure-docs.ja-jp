---
title: IoT Hub 用のデバイス更新で IoT プラグ アンド プレイが使用されるしくみを理解する | Microsoft Docs
description: IoT Hub 用のデバイス更新では、無線更新対応のデバイスを検出して管理するために、IoT プラグ アンド プレイが使用されます。
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 227488f165aaad2f204c647eed17467a4ef561a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660682"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT Hub 用のデバイス更新と IoT プラグ アンド プレイ

IoT Hub 用のデバイス更新では、無線更新対応のデバイスを検出して管理するために、[IoT プラグ アンド プレイ](https://docs.microsoft.com/azure/iot-pnp/)が使用されます。 デバイス更新サービスにより、PnP インターフェイスを使用して、デバイスとの間でプロパティとメッセージが送受信されます。 IoT Hub 用のデバイス更新を使用するには、以下で説明するように、IoT デバイスで次のインターフェイスとモデル ID を実装する必要があります。

## <a name="adu-core-interface"></a>ADU Core インターフェイス

"ADUCoreInterface" インターフェイスは、更新アクションとメタデータをデバイスに送信し、デバイスから更新状態を受信するために使用されます。 "ADU Core" インターフェイスは、2 つのオブジェクト プロパティに分割されます。

このインターフェイスを実装する場合、モデルで予想されるコンポーネント名は **"azureDeviceUpdateAgent"** です。 [Azure IoT PnP のコンポーネントの詳細を確認する](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

### <a name="agent-metadata"></a>エージェント メタデータ

エージェント メタデータには、デバイス更新サービスに情報と状態を送信するためにデバイスまたはデバイス更新エージェントによって使用されるフィールドが含まれています。

|名前|スキーマ|Direction|説明|例|
|----|------|---------|-----------|-----------|
|resultCode|整数 (integer)|デバイスからクラウド|前回の更新操作の結果に関する情報が含まれているコード。 成功または失敗のいずれでも設定でき、[HTTP 状態コードの仕様](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に従う必要があります。|500|
|extendedResultCode|整数 (integer)|デバイスからクラウド|結果に関する追加情報が含まれているコード。 成功または失敗のいずれでも設定できます。|0x80004005|
|state|整数 (integer)|デバイスからクラウド|デバイス更新エージェントの現在の状態を示す整数です。 詳細については、以下をご覧ください。 |アイドル|
|installedUpdateId|string|デバイスからクラウド|現在 (デバイス更新を通じて) インストールされている更新プログラムの ID。 デバイス更新によって更新が行われたことのないデバイスの場合、この値は null になります。|[Null]|
|`deviceProperties`|マップ|デバイスからクラウド|製造元とモデルが含まれるプロパティのセット。|詳細については、以下をご覧ください。

#### <a name="state"></a>State

デバイス更新サービスからアクションを受け取った後に、デバイス更新エージェントによって報告された状態です。 `State` は、 デバイス更新サービスからデバイス更新エージェントに送信された `Action` (後の `Actions` を参照) への応答で報告されます。 デバイス更新サービスとデバイス更新エージェントの間でやり取りされる要求の[ワークフローの概要](understand-device-update.md#device-update-agent)をご覧ください。

|名前|値|説明|
|---------|-----|-----------|
|アイドル|0|デバイスは、デバイス更新サービスからアクションを受け取る準備ができています。 更新が成功すると、状態は `Idle` に戻ります。|
|DownloadSucceeded|2|ダウンロード成功。|
|InstallSucceeded|4|インストール成功。|
|失敗|255|更新の間にエラーが発生しました。|

#### <a name="device-properties"></a>デバイスのプロパティ

製造元とモデルが含まれるプロパティのセットです。

|名前|スキーマ|Direction|説明|
|----|------|---------|-----------|
|manufacturer|string|デバイスからクラウド|`deviceProperties` によって報告されたデバイスの製造元。 このプロパティは、2 つの場所のいずれかから読み取られます。最初に "AzureDeviceUpdateCore" インターフェイスによって、[構成ファイル](device-update-configuration-file.md)から "aduc_manufacturer" 値の読み取りが試みられます。  値が構成ファイルに設定されていない場合、ADUC_DEVICEPROPERTIES_MANUFACTURER のコンパイル時の定義が既定で報告されます。 このプロパティは、起動時にのみ報告されます。|
|model|string|デバイスからクラウド|`deviceProperties` によって報告されたデバイスのデバイス モデル。 このプロパティは、2 つの場所のいずれかから読み取られます。最初に AzureDeviceUpdateCore インターフェイスによって、[構成ファイル](device-update-configuration-file.md)から "aduc_model" 値の読み取りが試みられます。  値が構成ファイルに設定されていない場合、ADUC_DEVICEPROPERTIES_MODEL のコンパイル時の定義が既定で報告されます。 このプロパティは、起動時にのみ報告されます。|
|aduVer|string|デバイスからクラウド|デバイスで実行されているデバイス更新エージェントのバージョン。 この値は、コンパイル時に ENABLE_ADU_TELEMETRY_REPORTING が 1 (true) に設定されている場合にのみ、ビルドから読み取られます。 お客様は、値を 0 (false) に設定することにより、バージョンの報告をオプトアウトすることができます。 [デバイス更新エージェントのプロパティをカスタマイズする方法](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)。|
|doVer|string|デバイスからクラウド|デバイスで実行されている配信最適化エージェントのバージョン。 この値は、コンパイル時に ENABLE_ADU_TELEMETRY_REPORTING が 1 (true) に設定されている場合にのみ、ビルドから読み取られます。 お客様は、値を 0 (false) に設定することにより、バージョンの報告をオプトアウトすることができます。[配信最適化エージェントのプロパティをカスタマイズする方法](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)。|

### <a name="service-metadata"></a>サービス メタデータ

サービス メタデータには、デバイス更新サービスによってアクションとデータをデバイス更新エージェントに伝えるために使用されるフィールドが含まれています。

|名前|スキーマ|Direction|説明|
|----|------|---------|-----------|
|action|整数 (integer)|クラウドからデバイス|エージェントが実行する必要のあるアクションに対応する整数です。 値は後の一覧のとおりです。|
|updateManifest|string|クラウドからデバイス|更新の内容を記述するために使用されます。 [マニフェストのインポート](import-update.md#create-device-update-import-manifest)から生成されます|
|updateManifestSignature|JSON オブジェクト|クラウドからデバイス|ソースの検証に使用される JSON Web Signature (JWS) と JSON Web キー。|
|fileUrls|マップ|クラウドからデバイス|`DownloadUri` への `FileHash` のマップ。 ダウンロードするファイルと、ファイルが正しくダウンロードされたことを検証するために使用するハッシュを、エージェントに指示します。|

#### <a name="action"></a>アクション

以下で示す `Actions` は、デバイス更新サービスによって指示されてデバイス更新エージェントによって実行されるアクションを表します。 デバイス更新エージェントにより、受信した `Action` の処理の `State` (上記の `State` のセクションを参照) が報告されます。 デバイス更新サービスとデバイス更新エージェントの間でやり取りされる要求の[ワークフローの概要](understand-device-update.md#device-update-agent)をご覧ください。

|名前|値|説明|
|---------|-----|-----------|
|ダウンロード|0|発行されたコンテンツまたは更新プログラム、および必要なその他のコンテンツをダウンロードします|
|インストール|1|コンテンツまたは更新プログラムをインストールします。 通常、これはコンテンツまたは更新プログラムのインストーラーの呼び出しを意味します。|
|適用|2|更新を完了します。 必要な場合は、システムに再起動が通知されます。|
|Cancel|255|現在のアクションの処理を停止し、`Idle` に戻ります。 `Failed` 状態のエージェントに `Idle` に戻るよう通知するためにも使用されます。|

## <a name="device-information-interface"></a>デバイス情報インターフェイス

デバイス情報インターフェイスは、[IoT プラグ アンド プレイ アーキテクチャ](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)で使用される概念です。 デバイスのハードウェアとオペレーティング システムに関する情報を提供する、デバイスからクラウドへのプロパティが含まれています。 IoT Hub 用のデバイス更新では、テレメトリと診断のために DeviceInformation.manufacturer と DeviceInformation.model プロパティが使用されます。 デバイス情報インターフェイスの詳細については、こちらの[例](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)をご覧ください。

このインターフェイスを実装する場合、モデルで予想されるコンポーネント名は **deviceInformation** です。 [Azure IoT PnP のコンポーネントについて確認する](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

|名前|種類|スキーマ|Direction|説明|例|
|----|----|------|---------|-----------|-----------|
|manufacturer|プロパティ|string|デバイスからクラウド|デバイスの製造元の会社名。 これは、相手先ブランド供給 (OEM) の名前と同じにすることができます。|Contoso|
|model|プロパティ|string|デバイスからクラウド|デバイス モデルの名前または ID。|IoT Edge デバイス|
|swVersion|プロパティ|string|デバイスからクラウド|デバイス上のソフトウェアのバージョン。 swVersion は、ファームウェアのバージョンである場合があります。|4.15.0-122|
|osName|プロパティ|string|デバイスからクラウド|デバイス上のオペレーティング システムの名前。|Ubuntu Server 18.04|
|processorArchitecture|プロパティ|string|デバイスからクラウド|デバイスのプロセッサのアーキテクチャ。|ARM64|
|processorManufacturer|プロパティ|string|デバイスからクラウド|デバイスのプロセッサの製造元の名前。|Microsoft|
|totalStorage|プロパティ|string|デバイスからクラウド|デバイスで使用可能な記憶域の合計 (キロバイト単位)。|2048|
|totalMemory|プロパティ|string|デバイスからクラウド|デバイスで使用可能なメモリの合計 (キロバイト単位)。|256|

## <a name="model-id"></a>モデル ID 

モデル ID は、IoT プラグ アンド プレイによってスマート デバイスから Azure IoT アプリケーションに機能が公開される方法です。Azure IoT アプリケーションに機能を公開するようにスマート デバイスを構築する方法の詳細については、「[IoT プラグ アンド プレイ デバイス開発者ガイド](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c)」をご覧ください。

IoT Hub 用のデバイス更新で、デバイス接続の一部として **"dtmi:AzureDeviceUpdate;1"** という値でモデル ID を通知するには、IoT プラグ アンド プレイ スマート デバイスが必要です。 [モデル ID を通知する方法を確認する](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c#model-id-announcement).
