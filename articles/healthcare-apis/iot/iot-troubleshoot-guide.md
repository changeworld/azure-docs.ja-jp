---
title: IoT コネクタのトラブルシューティング ガイドと方法 - Azure Healthcare API
description: この記事は、IoT コネクタの一般的なエラー メッセージ、条件、マッピング ファイルのコピー方法のトラブルシューティングに役立ちます。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jasteppe
ms.openlocfilehash: d5111425c6377cf16d3fbd4afceb7fcacc1e539a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077422"
---
# <a name="iot-connector-troubleshooting-guide"></a>IoT コネクタのトラブルシューティング ガイド

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、一般的な IoT コネクタのエラー メッセージと状態をトラブルシューティングする手順について説明します。 また、IoT コネクタの Device と 高速ヘルスケア相互運用性リソース (FHIR&#174;) 変換先マッピングのコピーを作成する方法も学習します。 また、デバイスマッピングコピーと FHIR 変換先マッピング コピーを使用して、デバイスの外部で編集およびアーカイブAzure portal。  

> [!TIP]
> IoT コネクタ [の Azure テクニカル](https://azure.microsoft.com/support/create-ticket/) サポート チケットを開く場合は、トラブルシューティング プロセスに役立つ Device と FHIR の宛先マッピングのコピーを含める必要があります。

## <a name="device-and-fhir-destination-mappings-validations"></a>デバイスと FHIR の宛先マッピングの検証

このセクションでは、IoT コネクタが実行する検証プロセスについて説明します。 検証プロセスでは、デバイスと FHIR の変換先マッピングを検証してから、それらを使用できるように保存します。 これらの要素は、Device と FHIR の変換先マッピングで必要です。

> [!TIP]
> IoT コネクタの Device と FHIR の変換先マッピングの編集、テスト、トラブルシューティングを行う [IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) コネクタ データ マッパー ツールを確認してください。 Azure portal で IoT コネクタにアップロードしたり、オープン ソース バージョンの IoT コネクタで使用したりするために [マッピングをエクスポート](https://github.com/microsoft/iomt-fhir) します。

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
>装着可能な IoMT デバイスをオンまたは削除した場合、IoT コネクタが一致して出力する名前を除き、要素には値が含まれます。 FHIR 変換では、IoT コネクタはセマンティック型に基づいてコード可能な概念にマップします。 つまり、実際の値は設定されません。

**FHIR 宛先マッピング**

|要素|必須|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> これは、現時点で検証された唯一の必須の FHIR 変換先マッピング要素です。

## <a name="error-messages-and-fixes"></a>エラー メッセージと修正プログラム

### <a name="iot-connector-resource"></a>IoT コネクタ リソース

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|リソースの種類 `iotconnectors` は、最大数に達しました。|API と Azure portal|IoT コネクタのサブスクリプション クォータに達しました (既定値はサブスクリプションあたり 25 です)。|IoT コネクタの既存のインスタンスのいずれかを削除します。 サブスクリプション クォータに達していない別のサブスクリプションを使用します。 サブスクリプション クォータを増やすように要求します。
|無効な `deviceMapping` マッピングです。 検証エラー: {エラーの一覧}|API と Azure portal|`properties.deviceMapping`IoT コネクタのリソース プロビジョニング要求で指定された は無効です。|`properties.deviceMapping` プロパティに指定されているマッピング JSON のエラーを修正します。
|`fullyQualifiedEventHubNamespace` が Null、空、または形式が正しくありません。|API と Azure portal|IoT コネクタのプロビジョニング要求 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` が無効です。|IoT コネクタを正しい `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 形式に更新します。 `{YOUR_NAMESPACE}.servicebus.windows.net` である必要があります。
|子リソースをプロビジョニングする前に、先祖リソースを完全にプロビジョニングする必要があります。|API|親ワークスペースはまだプロビジョニング中です。|親ワークスペースのプロビジョニングが完了するまで待ち、プロビジョニング要求を再度送信します。
|子リソースの `Location` プロパティは、親リソースの `Location` プロパティと一致する必要があります。|API|IoT コネクタプロビジョニング要求プロパティ `location` は、親ワークスペース プロパティとは異 `location` なります。|プロビジョニング要求の IoT コネクタの プロパティを、親ワークスペース プロパティと同じ `location` 値に設定 `location` します。

### <a name="destination-resource"></a>宛先リソース

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|リソースの種類 `iotconnectors/destinations` は、最大数に達しました。|API と Azure portal|IoT コネクタの宛先リソース クォータに達し、既定値は IoT コネクタごとに 1 です)。|IoT コネクタの宛先リソースの既存のインスタンスを削除します。 IoT コネクタごとに許可される宛先リソースは 1 つのみです。
|指定された `fhirServiceResourceId` は無効です。|API と Azure portal|宛先 `properties.fhirServiceResourceId` リソース プロビジョニング要求で指定された は、Azure Healthcare API FHIR サービスのインスタンスの有効なリソース ID ではありません。|リソース ID が正しく書式設定されていることを確認し、リソース ID が Azure Healthcare API FHIR インスタンス用に設定されていることを確認します。 形式は `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/` である必要があります。
|子リソースをプロビジョニングする前に、先祖リソースを完全にプロビジョニングする必要があります。|API|親ワークスペースまたは親 IoT コネクタはまだプロビジョニング中です。|親ワークスペースまたは親 IoT コネクタのプロビジョニングが完了するまで待ち、プロビジョニング要求を再度送信します。
|子リソースの `Location` プロパティは、親リソースの `Location` プロパティと一致する必要があります。|API|Destination provisioning request `location` プロパティは、親 IoT コネクタ プロパティとは異 `location` なります。|プロビジョニング要求の Destination の プロパティを、親 IoT コネクタ プロパティと同じ `location` 値に設定 `location` します。

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>IoT コネクタ データが FHIR サービスに表示されない理由

|潜在的な問題|修正|
|----------------|-----|
|データがまだ処理中です。|データはバッチで (最大 15 分ごとに) FHIR サービスに送信されます。  データがまだ処理中である可能性があります。また、データを FHIR サービスで永続化するにはさらに時間がかかります。|
|デバイスマッピングが構成されていません。|準拠しているデバイスのマッピングを構成して保存します。|
|FHIR の宛先マッピングが構成されていません。|準拠する FHIR 変換先マッピングを構成して保存します。|
|デバイスメッセージに、デバイスマッピングで定義された式が含まれていません。|デバイス `JsonPath` メッセージに定義されているデバイスマッピング一致トークンで定義されている式を確認します。|
|FHIR サービスにデバイス リソースが作成されていません (解決の種類: 検索のみ)*。|FHIR サービスに有効なデバイス リソースを作成します。 受信メッセージで指定されたデバイス ID と一致する識別子が、デバイス リソースに含まれていることを確認してください。|
|FHIR サービスに親リソースが作成されていません (解決の種類: 検索のみ)*。|FHIR サービスに有効な親リソースを作成します。|
|`Device.patient` 参照が設定されていないか、参照が無効です (解決の種類: 検索のみ)*。|患者リソースに対して有効な[参照](https://www.hl7.org/fhir/device-definitions.html#Device.patient)が、デバイス リソースに含まれていることを確認します。| 

* リファレンスクイックスタート: IoT コネクタの解決の種類に関する機能的な説明 (例: Lookup または Create) については、 [Azure portal を使用して iot コネクタをデプロイ](deploy-iot-connector-in-azure.md) します。

### <a name="the-operation-performed-by-iot-connector"></a>IoT コネクタによって実行される操作

このプロパティは、エラーが発生したときに IoT コネクタによって実行される操作を表します。 通常、操作はデバイス メッセージ処理中のデータ フロー ステージを表します。 このプロパティの可能な値の一覧を次に示します。

> [!NOTE]
> IoT コネクタのデータフローのさまざまな段階については、「 [iot コネクタのデータフロー](iot-data-flow.md)」を参照してください。

|データ フロー ステージ|Description|
|---------------|-----------|
|セットアップ|セットアップデータフローステージは、IoT コネクタのインスタンスの設定に固有の操作です。|
|正規化|正規化は、デバイス データが正規化されるデータ フロー ステージです。|
|グループ化|正規化されたデータがグループ化されるグループ化データ フロー ステージ。|
|FHIRConversion|FHIRConversion は、グループ化された正規化されたデータを FHIR リソースに変換するデータフローステージです。|
|Unknown|Unknown は、エラーが発生したときの不明な操作の種類です。|

### <a name="the-severity-of-the-error"></a>エラーの重大度

このプロパティは、発生したエラーの重大度を表します。 このプロパティの可能な値の一覧を次に示します。

|重大度|説明|
|---------------|-----------|
|警告|データ フロー プロセスにはいくつかの軽微な問題が存在しますが、デバイス メッセージの処理は停止しません。|
|エラー|このメッセージは、特定のデバイス メッセージの処理でエラーが発生し、他のメッセージは想定どおりに実行が継続される可能性があるときに発生します。|
|重大|このエラーは、IoT コネクタにシステムレベルの問題が存在し、処理する必要があるメッセージがない場合に発生します。|

### <a name="the-type-of-error"></a>エラーの種類

このプロパティは、特定のエラーのカテゴリを示します。これは基本的に、種類が類似したエラーの論理的なグループを表します。 このプロパティの可能な値の一覧を次に示します。

|エラーの種類|Description|
|----------|-----------|
|`DeviceTemplateError`|このエラーの種類は、デバイスマッピングに関連しています。|
|`DeviceMessageError`|このエラーの種類は、特定のデバイス メッセージを処理するときに発生します。|
|`FHIRTemplateError`|このエラーの種類は、FHIR の宛先マッピングに関連しています|
|`FHIRConversionError`|このエラーの種類は、メッセージを FHIR リソースに変換するときに発生します。|
|`FHIRResourceError`|このエラーの種類は、IoT コネクタによって参照される FHIR サービスの既存のリソースに関連しています。|
|`FHIRServerError`|このエラーの種類は、FHIR サービスと通信するときに発生します。|
|`GeneralError`|このエラーの種類は、他のすべての種類のエラーに関するものです。|

### <a name="the-name-of-the-error"></a>エラーの名前

このプロパティは、特定のエラーの名前を示します。 すべてのエラー名、それらの説明、関連するエラーの種類、重大度、データ フロー ステージの一覧を次に示します。

|エラー名|Description|エラーの種類|エラーの重大度|データ フロー ステージ|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|このエラーは、デバイスメッセージに存在する各識別子について、FHIR サービスに複数の患者またはデバイスのリソースが見つかった場合に発生します。|`FHIRResourceError`|エラー|`FHIRConversion`|
|`TemplateNotFoundException`|IoT コネクタのインスタンスで構成されていないデバイスまたは FHIR の宛先マッピング。|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|デバイスマッピングに相関 ID が指定されていません。 `CorrelationIdNotDefinedException` は、FHIR 観測が、相関 ID を使用してデバイスの測定値をグループ化する必要がある場合に、正しく構成されていないときにのみ発生する条件付きエラーです。|`DeviceMessageError`|エラー|正規化|
|`PatientDeviceMismatchException`|このエラーは、FHIR サービスのデバイスリソースに患者リソースへの参照が含まれている場合に発生します。 このエラーの種類は、メッセージ内に存在する患者 ID と一致しないことを意味します。|`FHIRResourceError`|エラー|`FHIRConversionError`|
|`PatientNotFoundException`|デバイス メッセージ内に存在するデバイス ID に関連付けられた Device FHIR リソースによって参照されている Patient FHIR リソースはありません。 注このエラーは、IoT コネクタインスタンスが *参照* 解像度の種類で構成されている場合にのみ発生します。|`FHIRConversionError`|エラー|`FHIRConversion`|
|`DeviceNotFoundException`|デバイスメッセージに存在するデバイス id に関連付けられたデバイスリソースが FHIR サービスに存在しません。|`DeviceMessageError`|エラー|正規化|
|`PatientIdentityNotDefinedException`|このエラーは、デバイスメッセージの患者 id を解析する式がデバイスのマッピングで構成されていない場合、またはデバイスメッセージに患者の識別子が存在しない場合に発生します。 注このエラーは、IoT コネクタの解決の種類が *作成* に設定されている場合にのみ発生します。|`DeviceTemplateError`|Critical|正規化|
|`DeviceIdentityNotDefinedException`|このエラーは、デバイスメッセージからデバイス id を解析する式がデバイスマッピングで構成されていない場合、またはデバイスの識別子がデバイスメッセージに存在しない場合に発生します。|`DeviceTemplateError`|Critical|正規化|
|`NotSupportedException`|サポートされていない形式のデバイス メッセージを受信したときにエラーが発生しました。|`DeviceMessageError`|エラー|正規化|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>IoT コネクタデバイスと FHIR の宛先マッピングのコピーを作成する

IoT コネクタマッピングをコピーすると、Azure portal web サイトの外部での編集とアーカイブに役立ちます。

トラブルシューティングプロセスに役立つサポートチケットを開いている場合は、Azure テクニカルサポートにマッピングコピーを提供する必要があります。

> [!NOTE]
> 現時点では、デバイスおよび FHIR の宛先マッピングでサポートされている形式は JSON だけです。

> [!TIP]
> IoT コネクタの[デバイスマッピング](how-to-use-device-mapping-iot.md)と[fhir の宛先マッピング](how-to-use-fhir-mapping-iot.md)に関する詳細情報

1. [医療 Api] ワークスペースの左側にある [ **IoT コネクタ]** を選択します。

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="[IoT コネクタ] を選択します。" lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. デバイスと FHIR の移行先のマッピングをコピーする **IoT コネクタ** の名前を選択します。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > このプロセスは、 **"destination"** fhir 変換先マッピングの内容のコピーと保存にも使用できます。

3. JSON の内容を選択し、コピー操作を行います (例: **Ctrl + C** キーを押す)。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT コネクタ4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. 貼り付け操作 (たとえば、**Ctrl + V** キーを押す) を Microsoft Visual Studio Code やメモ帳などのエディター内の新しいファイルに実行します。 .json 拡張子を使用してファイル **を保存してください** 。

> [!TIP]
> IoT コネクタ用の [Azure テクニカル](https://azure.microsoft.com/support/create-ticket/) サポート チケットを開く場合は、トラブルシューティング プロセスに役立つ Device と FHIR の宛先マッピングのコピーを必ず含める必要があります。

## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。