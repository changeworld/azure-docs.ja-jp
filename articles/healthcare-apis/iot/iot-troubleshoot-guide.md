---
title: Azure IoT Connector for FHIR (プレビュー) - トラブルシューティング ガイドと操作方法
description: この記事は、一般的なエラー メッセージ、状態、マッピング ファイルのコピー方法のトラブルシューティングに役立ちます。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 07/20/2021
ms.author: jasteppe
ms.openlocfilehash: 0edc58bd10cfdab0b7b6cff29c3513ab3c73025b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781101"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT Connector for FHIR (プレビュー) のトラブルシューティング ガイド

この記事では、一般的な Azure IoT Connector for FHIR&#174; (高速ヘルスケア相互運用性リソース) のエラー メッセージと状態をトラブルシューティングする手順について説明します。 また、デバイスや FHIR などの Azure IoT Connector for FHIR (プレビュー) 変換マッピング JSON テンプレートのコピーを作成する方法も説明します。 また、Azure portal 外での編集とアーカイブに、変換マッピング JSON テンプレートを使用することもできます。  

> [!NOTE]
> この記事の残りの部分では、Azure IoT Connector for FHIR (プレビュー) を IoT Connector と呼びます。

> [!TIP]
> IoT Connector の [Azure テクニカル サポート](https://azure.microsoft.com/support/create-ticket/) チケットを開く場合は、トラブルシューティング プロセスに役立つように、必ず変換マッピング JSON のコピーを含めるようにしてください。

## <a name="device-and-fhir-conversion-mapping-json-template-validations"></a>デバイスと FHIR 変換マッピング JSON テンプレートの検証

このセクションでは、IoT Connector が実行する検証処理について説明します。 検証プロセスでは、デバイス テンプレートと FHIR 変換マッピング JSON テンプレートが使用のために保存できるようになる前に、これらを検証します。 これらの要素は、デバイスおよび FHIR 変換マッピング JSON に必要です。

**デバイス マッピング**

|要素|必須|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|True|
|Values[].ValueExpression|True|

> [!NOTE]
> `Values[].ValueName and Values[].ValueExpression` 要素は、配列に値エントリがある場合にのみ必要です。 値がマップされない場合は有効です。 これは、送信されるテレメトリがイベントである場合に使用されます。 
>
>次に例を示します。
> 
>ウェアラブル IoMT デバイスを装着した、または削除した場合、要素には、IoT Connector が照合して出力する名前を除き、値は含まれません。 FHIR 変換では、IoT Connector は、それをセマンティックの種類に基づいてコード可能な概念にマップします。 つまり、実際の値は設定されません。

**FHIR マッピング**

|要素|必須|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> これは、現時点で検証されている唯一の必須の FHIR マッピング要素です。

## <a name="error-messages-and-fixes"></a>エラー メッセージと修正プログラム

### <a name="iot-connector-resource"></a>IoT Connector リソース

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|リソースの種類 `iotconnectors` は、最大数に達しました。|API と Azure portal|Azure IoT Connector のサブスクリプション クォータに達し、サブスクリプションあたりの既定値は 25 です。|IoT Connector の既存のインスタンスの 1 つを削除します。 サブスクリプション クォータに達していない別のサブスクリプションを使用します。 サブスクリプション クォータを増やすように要求します。
|無効な `deviceMapping` マッピングです。 検証エラー: {エラーの一覧}|API と Azure portal|`properties.deviceMapping` が IoT Connector リソース プロビジョニングで提供した要求は無効です。|`properties.deviceMapping` プロパティに指定されているマッピング JSON のエラーを修正します。
|`fullyQualifiedEventHubNamespace` が Null、空、または形式が正しくありません。|API と Azure portal|IoT Connector プロビジョニング要求 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` が無効です。|IoT Connector `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` を正しい形式に更新します。 `{YOUR_NAMESPACE}.servicebus.windows.net` である必要があります。
|子リソースをプロビジョニングする前に、先祖リソースを完全にプロビジョニングする必要があります。|API|親ワークスペースはまだプロビジョニング中です。|親ワークスペースのプロビジョニングが完了するまで待ち、プロビジョニング要求を再度送信します。
|子リソースの `Location` プロパティは、親リソースの `Location` プロパティと一致する必要があります。|API|IoT Connector プロビジョニング要求 `location` プロパティは、親ワークスペース `location` プロパティと異なります。|プロビジョニング要求内の IoT Connector の `location` プロパティを、親ワークスペース `location` プロパティと同じ値に設定します。

### <a name="destination-resource"></a>宛先リソース

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|リソースの種類 `iotconnectors/destinations` は、最大数に達しました。|API と Azure portal|IoT Connector 宛先リソース クォータに達し、IoT Connector ごとの既定値は 1 です。|IoT Connector 宛先の既存のインスタンスを削除します。 IoT Connector につき、1 つの宛先リソースのみが許可されます。
|指定された `fhirServiceResourceId` は無効です。|API と Azure portal|宛先リソース プロビジョニング要求で指定された `properties.fhirServiceResourceId` は、Azure API for FHIR インスタンスの有効なリソース ID ではありません。|リソース ID が正しい形式であることを確認し、リソース ID が Azure API for FHIR インスタンス用であることを確認します。 形式は `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/` である必要があります。
|子リソースをプロビジョニングする前に、先祖リソースを完全にプロビジョニングする必要があります。|API|親ワークスペースまたは親 IoT Connector はまだプロビジョニング中です。|親ワークスペースまたは親 IoT Connector のプロビジョニングが完了するまで待ち、プロビジョニング要求を再度送信します。
|子リソースの `Location` プロパティは、親リソースの `Location` プロパティと一致する必要があります。|API|宛先プロビジョニング要求 `location` プロパティは、親 IoT Connector `location` プロパティと異なります。|プロビジョニング要求内の宛先の `location` プロパティを、親 IoT Connector `location` プロパティと同じ値に設定します。

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-fhir-service"></a>Azure IoT Connector for FHIR (プレビュー) データが、FHIR サービスに表示されないのはなぜですか?

|潜在的な問題|修正|
|----------------|-----|
|データがまだ処理中です。|データはバッチで (最大 15 分ごとに) FHIR サービスに送信されます。  データがまだ処理中である可能性があります。また、データを FHIR サービスで永続化するにはさらに時間がかかります。|
|デバイス変換マッピング JSON が構成されていません。|準拠するデバイス変換マッピング JSON を構成して保存します。|
|FHIR 変換マッピング JSON が構成されていません。|準拠する FHIR 変換マッピング JSON を構成して保存します。|
|デバイス マッピングで定義されている必要な式が、デバイス メッセージに含まれていません。|デバイス マッピングに定義されている `JsonPath` 式が、デバイス メッセージに定義されているトークンと一致することを確認します。|
|FHIR サービスにデバイス リソースが作成されていません (解決の種類: 検索のみ)*。|FHIR サービスに有効なデバイス リソースを作成します。 受信メッセージで指定されたデバイス ID と一致する識別子が、デバイス リソースに含まれていることを確認してください。|
|FHIR サービスに親リソースが作成されていません (解決の種類: 検索のみ)*。|FHIR サービスに有効な親リソースを作成します。|
|`Device.patient` 参照が設定されていないか、参照が無効です (解決の種類: 検索のみ)*。|患者リソースに対して有効な[参照](https://www.hl7.org/fhir/device-definitions.html#Device.patient)が、デバイス リソースに含まれていることを確認します。| 

\* [クイックスタート: Azure portal を使用する Azure IoT コネクタ (プレビュー) のデプロイ](deploy-iot-connector-in-azure.md)に関するページを参照し、Azure IoT Connector for FHIR の解決の種類 (例: 参照または作成) の機能説明を確認してください。

### <a name="the-operation-performed-by-the-iot-connector"></a>IoT Connector によって実行される操作

このプロパティは、エラー発生時に IoT Connector で実行中の操作を表します。 通常、操作はデバイス メッセージ処理中のデータ フロー ステージを表します。 このプロパティの可能な値の一覧を次に示します。

> [!NOTE]
> IoT Connector でのデータ フローの異なるステージについては、「[Azure IoT Connector for FHIR のデータ フロー](iot-data-flow.md)」を参照してください。

|データ フロー ステージ|Description|
|---------------|-----------|
|セットアップ|セットアップ データ フロー ステージは、IoT Connector のインスタンスの設定に固有の操作です。|
|正規化|正規化は、デバイス データが正規化されるデータ フロー ステージです。|
|グループ化|正規化されたデータがグループ化されるグループ化データ フロー ステージ。|
|FHIRConversion|FHIRConversion は、正規化されてグループ化されたデータが FHIR リソースに変換されるデータ フロー ステージです。|
|Unknown|Unknown は、エラーが発生したときの不明な操作の種類です。|

### <a name="the-severity-of-the-error"></a>エラーの重大度

このプロパティは、発生したエラーの重大度を表します。 このプロパティの可能な値の一覧を次に示します。

|重大度|説明|
|---------------|-----------|
|警告|データ フロー プロセスにはいくつかの軽微な問題が存在しますが、デバイス メッセージの処理は停止しません。|
|エラー|このメッセージは、特定のデバイス メッセージの処理でエラーが発生し、他のメッセージは想定どおりに実行が継続される可能性があるときに発生します。|
|重大|このエラーは、IoT Connector にシステム レベルの問題がいくつか存在し、メッセージは処理されないときです。|

### <a name="the-type-of-the-error"></a>エラーの種類

このプロパティは、特定のエラーのカテゴリを示します。これは基本的に、種類が類似したエラーの論理的なグループを表します。 このプロパティの可能な値の一覧を次に示します。

|エラーの種類|Description|
|----------|-----------|
|`DeviceTemplateError`|このエラーの種類は、デバイス マッピング テンプレートに関連しています。|
|`DeviceMessageError`|このエラーの種類は、特定のデバイス メッセージを処理するときに発生します。|
|`FHIRTemplateError`|このエラーの種類は、FHIR マッピング テンプレートに関連しています。|
|`FHIRConversionError`|このエラーの種類は、メッセージを FHIR リソースに変換するときに発生します。|
|`FHIRResourceError`|このエラーの種類は、IoT Connector によって参照されている FHIR サーバー内の既存のリソースに関連します。|
|`FHIRServerError`|このエラーの種類は、FHIR サーバーと通信するときに発生します。|
|`GeneralError`|このエラーの種類は、他のすべての種類のエラーに関するものです。|

### <a name="the-name-of-the-error"></a>エラーの名前

このプロパティは、特定のエラーの名前を示します。 すべてのエラー名、それらの説明、関連するエラーの種類、重大度、データ フロー ステージの一覧を次に示します。

|エラー名|Description|エラーの種類|エラーの重大度|データ フロー ステージ|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|このエラーは、デバイス メッセージ内に存在する個々の ID に対して FHIR サーバーで患者またはデバイスのリソースが複数見つかったときに発生します。|`FHIRResourceError`|エラー|`FHIRConversion`|
|`TemplateNotFoundException`|IoT Connector のインスタンスにデバイスまたは FHIR マッピング テンプレートが構成されていません。|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|デバイス マッピング テンプレートに相関 ID が指定されていません。 `CorrelationIdNotDefinedException` は、FHIR 観測が、相関 ID を使用してデバイスの測定値をグループ化する必要がある場合に、正しく構成されていないときにのみ発生する条件付きエラーです。|`DeviceMessageError`|エラー|正規化|
|`PatientDeviceMismatchException`|このエラーは、FHIR サーバー上のデバイス リソースから患者リソースへの参照がある場合に発生します。 このエラーの種類は、メッセージ内に存在する患者 ID と一致しないことを意味します。|`FHIRResourceError`|エラー|`FHIRConversionError`|
|`PatientNotFoundException`|デバイス メッセージ内に存在するデバイス ID に関連付けられた Device FHIR リソースによって参照されている Patient FHIR リソースはありません。 このエラーは、IoT Connector インスタンスに "*検索*" という解決の種類が構成されている場合にのみ発生することに注意してください。|`FHIRConversionError`|エラー|`FHIRConversion`|
|`DeviceNotFoundException`|デバイス メッセージ内に存在するデバイス ID に関連付けられたデバイス リソースが FHIR サーバー上に存在しません。|`DeviceMessageError`|エラー|正規化|
|`PatientIdentityNotDefinedException`|このエラーは、デバイス メッセージの患者 ID を解析する式がデバイス マッピング テンプレートに構成されていないか、デバイス メッセージ内に患者 ID が存在しない場合に発生します。 このエラーは、IoT Connector の解決の種類が "*作成*" に設定されている場合にのみ発生することに注意してください。|`DeviceTemplateError`|Critical|正規化|
|`DeviceIdentityNotDefinedException`|このエラーは、デバイス メッセージのデバイス ID を解析する式がデバイス マッピング テンプレートに構成されていないか、デバイス メッセージ内にデバイス ID が存在しない場合に発生します。|`DeviceTemplateError`|Critical|正規化|
|`NotSupportedException`|サポートされていない形式のデバイス メッセージを受信したときにエラーが発生しました。|`DeviceMessageError`|エラー|正規化|

## <a name="creating-copies-of-the-iot-connector-conversion-mapping-json"></a>IoT Connector 変換マッピング JSON のコピーの作成

IoT Connector マッピング ファイルのコピーは、Azure portal Web サイト外での編集とアーカイブに役立ちます。

トラブルシューティング処理に役立つように、サポート チケットを開くときにマッピング ファイルのコピーを Azure テクニカル サポートに提供する必要があります。

> [!NOTE]
> 現時点でデバイスおよび FHIR マッピング ファイルでサポートされている形式は、JSON のみです。

> [!TIP]
> Azure IoT Connector for FHIR の[デバイスおよび FHIR 変換マッピング JSON](how-to-use-fhir-mapping-iot.md) の詳細を確認してください

1. Azure Healthcare API ワークスペースの左側にある **"IoT Connector (プレビュー)"** ブレードを選択します。

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="IoT Connector ブレードを選択します。" lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. 変換マッピング JSON のコピー元となる **IoT Connector** の名前を選択します。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> このプロセスは、 **[Configure FHIR mapping]\(FHIR マッピングの構成\)** JSON の内容をコピーして保存する場合にも使用できます。

3. JSON の内容を選択し、コピー操作を行います (例: **Ctrl + C** キーを押す)。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. 貼り付け操作 (たとえば、**Ctrl + V** キーを押す) を Microsoft Visual Studio Code やメモ帳などのエディター内の新しいファイルに実行します。 拡張子が **.json** のファイルが保存されていることを確認します。

> [!TIP]
> Azure IoT Connector for FHIR の [Azure テクニカル サポート](https://azure.microsoft.com/support/create-ticket/) チケットを開く場合は、トラブルシューティング プロセスに役立つように、必ず変換マッピング JSON のコピーを含めるようにしてください。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[Azure IoT Connector の概要](iot-connector-overview.md)

FHIR は [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 の許可を得て使用しています。
