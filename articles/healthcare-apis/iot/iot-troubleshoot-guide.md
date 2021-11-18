---
title: IoT コネクタのトラブルシューティングガイドと Azure の医療 Api
description: この記事は、IoT コネクタの一般的なエラーメッセージ、条件、およびマッピングファイルをコピーする方法をユーザーがトラブルシューティングするのに役立ちます。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 6a3af00eaca90c2ecdbbdcc4f6cce5231ffefcc0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719449"
---
# <a name="iot-connector-troubleshooting-guide"></a>IoT コネクタのトラブルシューティングガイド

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。 

> [!TIP]
> IoT コネクタの [Azure テクニカルサポート](https://azure.microsoft.com/support/create-ticket/) チケットを開くときに、デバイスのコピーと FHIR の移行先のマッピングを追加して、トラブルシューティングプロセスを支援します。

この記事では、IoT コネクタの一般的なエラーメッセージと条件をトラブルシューティングする手順について説明します。 また、IoT コネクタのデバイスと高速医療相互運用性リソース (FHIR&#174;) の宛先マッピングのコピーを作成する方法についても説明します。 また、デバイスおよび FHIR の宛先マッピングのコピーを使用して、Azure portal の外部で編集およびアーカイブを行うこともできます。 

## <a name="device-and-fhir-destination-mappings-validations"></a>デバイスおよび FHIR の宛先マッピングの検証

このセクションでは、IoT コネクタが行う検証プロセスについて説明します。 検証プロセスでは、デバイスおよび FHIR の宛先マッピングが検証されてから、使用のために保存されるようになります。 これらの要素は、デバイスおよび FHIR の変換先のマッピングに必要です。

> [!TIP]
> IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングについては、 [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) ツールをご覧ください。 Azure portal で IoT コネクタにアップロードするか、 [オープンソースバージョン](https://github.com/microsoft/iomt-fhir) の iot コネクタで使用するためのマッピングをエクスポートします。

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
>ウェアラブル IoMT デバイスを配置または削除すると、その要素には、IoT コネクタが一致して出力する名前以外の値はありません。 FHIR 変換では、IoT コネクタは、セマンティック型に基づいてコード可能な概念にマップします。 つまり、実際の値は設定されません。

**FHIR 宛先マッピング**

|要素|必須|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> これは、現時点で検証されている必須の FHIR 変換先マッピング要素だけです。

## <a name="error-messages-and-fixes"></a>エラー メッセージと修正プログラム

### <a name="iot-connector-resource"></a>IoT コネクタのリソース

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|リソースの種類 `iotconnectors` は、最大数に達しました。|API と Azure portal|IoT コネクタサブスクリプションのクォータに達しました (サブスクリプションあたりの既定値は 25)。|IoT コネクタの既存のインスタンスの1つを削除します。 サブスクリプション クォータに達していない別のサブスクリプションを使用します。 サブスクリプション クォータを増やすように要求します。
|無効な `deviceMapping` マッピングです。 検証エラー: {エラーの一覧}|API と Azure portal|`properties.deviceMapping`IoT コネクタのリソースプロビジョニング要求に指定されたが無効です。|`properties.deviceMapping` プロパティに指定されているマッピング JSON のエラーを修正します。
|`fullyQualifiedEventHubNamespace` が Null、空、または形式が正しくありません。|API と Azure portal|IoT コネクタのプロビジョニング要求 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` が無効です。|IoT コネクタ `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` を正しい形式に更新します。 `{YOUR_NAMESPACE}.servicebus.windows.net` である必要があります。
|子リソースをプロビジョニングする前に、先祖リソースを完全にプロビジョニングする必要があります。|API|親ワークスペースはまだプロビジョニング中です。|親ワークスペースのプロビジョニングが完了するまで待ち、プロビジョニング要求を再度送信します。
|子リソースの `Location` プロパティは、親リソースの `Location` プロパティと一致する必要があります。|API|IoT コネクタのプロビジョニング要求の `location` プロパティは、親のワークスペースプロパティとは異なり `location` ます。|`location`プロビジョニング要求の IoT コネクタのプロパティを、親ワークスペースプロパティと同じ値に設定し `location` ます。

### <a name="destination-resource"></a>宛先リソース

|Message|表示|条件|Fix| 
|-------|---------|---------|---|
|リソースの種類 `iotconnectors/destinations` は、最大数に達しました。|API と Azure portal|IoT コネクタの宛先リソースのクォータに達しました。既定値は IoT コネクタごとに1つです。|IoT コネクタの宛先リソースの既存のインスタンスを削除します。 1つの IoT コネクタに対して許可される送信先リソースは1つだけです。
|指定された `fhirServiceResourceId` は無効です。|API と Azure portal|`properties.fhirServiceResourceId`宛先リソースのプロビジョニング要求で指定されたが、Azure 医療 API FHIR サービスのインスタンスの有効なリソース ID ではありません。|リソース ID が正しくフォーマットされていることを確認し、リソース ID が Azure の医療 Api FHIR インスタンスのものであることを確認してください。 形式は `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/` である必要があります。
|子リソースをプロビジョニングする前に、先祖リソースを完全にプロビジョニングする必要があります。|API|親ワークスペースまたは親 IoT コネクタがまだプロビジョニングされています。|親ワークスペースまたは親 IoT コネクタのプロビジョニングが完了するまで待ってから、プロビジョニング要求を再度送信してください。
|子リソースの `Location` プロパティは、親リソースの `Location` プロパティと一致する必要があります。|API|宛先のプロビジョニング要求の `location` プロパティが、親の IoT コネクタ `location` プロパティと異なります。|`location`プロビジョニング要求の転送先のプロパティを、親の IoT コネクタプロパティと同じ値に設定し `location` ます。

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>IoT コネクタデータが FHIR サービスに表示されないのはなぜですか。

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
> IoT コネクタの[デバイスマッピング](how-to-use-device-mappings.md)と[fhir の宛先マッピング](how-to-use-fhir-mappings.md)に関する詳細情報

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

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。
