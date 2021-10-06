---
title: ワークフローを使用して Azure IoT Central アプリケーションを他のクラウド サービスと統合する |Microsoft Docs
description: このハウツー記事では、作成者として、IoT Central アプリケーションを他のクラウド サービスと統合するためのルールとアクションを構成する方法について説明します。 高度なルールを作成するには、Power Automate または Azure Logic Apps で IoT Central コネクタを使用します。
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1068bd5f5a97c4eeab702add9c305b38a8084464
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079673"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>ワークフローを使用して Azure IoT Central アプリケーションを他のクラウド サービスと統合する

IoT Central では、テレメトリベースの条件 (デバイスの温度がしきい値を超えたなど) に応じて、電子メール送信などのアクションをトリガーするルールを作成できます。

Power Automate と Azure Logic Apps 用の Azure IoT Central V3 コネクタを使用することで、IoT Central での操作を自動化する高度なルールを作成できます。

- Azure IoT Central アプリでルールが起動された際に、Power Automate または Azure Logic Apps でワークフローがトリガーされるようにすることができます。 これらのワークフローにより、他のクラウド サービス (Microsoft 365 など) や、サードパーティのサービスでアクションを実行できます。
- 別のクラウド サービス内 (Microsoft 365 など) のイベントにより、Power Automate または Azure Logic Apps でワークフローをトリガーすることができます。 これらのワークフローにより、アクションを実行したり、IoT Central アプリケーションからデータを取得したりできます。
- Azure IoT Central V3 コネクタは、一般提供されている [1.0 REST API](/rest/api/iotcentral/) サーフェスと適合しています。 すべてのコネクタ アクションは [DTDLv2 形式](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) をサポートしており、DTDLv1 ベース モデルのサポートは非推奨になります。 最新情報と最新の更新プログラムの詳細については、現在のコネクタ バージョンの [リリース ノート](/connectors/azureiotcentral/#release-notes) を参照してください。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を完了するには、次が必要です。

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

> [!NOTE]
> バージョン 2 の IoT Central アプリケーションを使用している場合は、前のバージョンのドキュメント サイトで「[Azure Logic Apps 内で IoT Central コネクタを使用してワークフローを作成する](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps)」を参照し、Azure IoT Central V2 コネクタを使用してください

## <a name="trigger-a-workflow-from-a-rule"></a>ルールからワークフローをトリガーする

Power Automate または Azure Logic Apps でワークフローがトリガーされるようにするには、まず IoT Central アプリケーションでルールを構成する必要があります。 詳細については、「[Azure IoT Central でルールとアクションを構成する](./howto-configure-rules.md)」を参照してください。

Power Automate のトリガーとして **Azure IoT Central V3** コネクタを追加するには:

1. Power Automate で **[+ 作成]** を選択し、 **[カスタム]** タブを選択します。
1. *IoT Central* を検索し、**Azure IoT Central V3** コネクタを選択します。
1. トリガーの一覧で、 **[ルールが起動されたとき (プレビュー)]** を選択します。
1. **[ルールが起動されたとき]** のステップで、IoT Central アプリケーションと、使用するルールを選択します。

Azure Logic Apps のトリガーとして **Azure IoT Central V3** コネクタを追加するには:

1. **Logic Apps デザイナー** で、 **[空のロジック アプリ]** テンプレートを選択します。
1. デザイナーで、*IoT Central* を検索し、**Azure IoT Central V3** コネクタを選択します。
1. トリガーの一覧で、 **[ルールが起動されたとき (プレビュー)]** を選択します。
1. **[ルールが起動されたとき]** のステップで、IoT Central アプリケーションと、使用するルールを選択します。

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Azure IoT Central - preview コネクタを検索し、トリガーを選択する":::

ワークフローにステップを追加して、統合シナリオを拡張することもできます。

## <a name="run-an-action"></a>アクションを実行する

Power Automate と Azure Logic Apps のワークフローから、IoT Central アプリケーションのアクションを実行することができます。 まずは、ワークフローを作成し、コネクタを使用して、ワークフローを開始するためのトリガーを定義します。 その後、**Azure IoT Central V3** コネクタをアクションとして使用します。

Power Automate のアクションとして **Azure IoT Central V3** コネクタを追加するには:

1. Power Automate の **[アクションを選択してください]** パネルで、 **[カスタム]** タブを選択します。
1. *IoT Central* を検索し、**Azure IoT Central V3** コネクタを選択します。
1. アクションの一覧で、使用する IoT Central アクションを選択します。
1. アクション ステップで、選択したアクションの構成を完了します。 次に、 **[保存]** を選択します。

Azure Logic Apps のアクションとして **Azure IoT Central V3 - preview** コネクタを追加するには:

1. **Logic Apps デザイナー** の **[アクションを選択してください]** パネルで、 **[カスタム]** タブを選択します。
1. *IoT Central* を検索し、**Azure IoT Central V3 - preview** コネクタを選択します。
1. アクションの一覧で、使用する IoT Central アクションを選択します。
1. アクション ステップで、選択したアクションの構成を完了します。 次に、 **[保存]** を選択します。

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Azure IoT Central V3 コネクタを検索し、アクションを選択する":::

## <a name="list-of-actions"></a>アクションの一覧

コネクタでサポートされているアクションの完全な一覧については、「[アクション](/connectors/azureiotcentral/#actions)」を参照してください。


### <a name="create-or-update-a-device"></a>ドメインを作成または更新する

IoT Central アプリケーションでデバイスを作成または更新するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 作成または更新するデバイスの一意の ID。 |
| Approved | IoT Central に接続することについて、デバイスが承認されているかどうかを選択します。 |
| デバイスの説明 | デバイスの詳細な説明。 |
| デバイス名 | デバイスの表示名。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |
| シミュレート済み | デバイスがシミュレートされるかどうかを選択します。 |

### <a name="delete-a-device"></a>デバイスの削除

IoT Central アプリケーションからデバイスを削除するには、この操作を使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |

### <a name="execute-a-device-command"></a>デバイス コマンドの実行

デバイスのインターフェイスのいずれかで定義されているコマンドを実行するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |
| デバイス コンポーネント | コマンドが含まれている、デバイス テンプレート内のインターフェイス。 |
| デバイス コマンド | 選択したインターフェイスのコマンドのいずれかを選択します。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |
| デバイス コマンド要求ペイロード | コマンドに要求ペイロードが必要な場合は、ここに追加します。  |

> [!NOTE]
> デバイス テンプレートを選択するまで、デバイス コンポーネントを選択することはできません。

### <a name="get-a-device-by-id"></a>ID でデバイスを取得する

デバイスの詳細を取得するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |

返された詳細は、他のアクションの動的な式で使用できます。 返されるデバイス詳細は次のとおりです。**承認済み**、**本文**、**デバイスの説明**、**デバイス名**、**デバイス テンプレート**、**プロビジョニング済み**、**シミュレート済み**。

### <a name="get-device-cloud-properties"></a>デバイス クラウドのプロパティを取得する

特定のデバイスのクラウド プロパティ値を取得するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |

返されたクラウド プロパティ値は、他のアクションの動的な式で使用できます。

### <a name="get-device-properties"></a>デバイスのプロパティを取得する

特定のデバイスのプロパティ値を取得するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |

返されたプロパティ値は、他のアクションの動的な式で使用できます。

### <a name="get-device-telemetry-value"></a>デバイスのテレメトリ値を取得する

特定のデバイスのテレメトリ値を取得するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |

返されたテレメトリ値は、他のアクションの動的な式で使用できます。

### <a name="update-device-cloud-properties"></a>デバイス クラウドのプロパティを更新する

特定のデバイスのクラウド プロパティ値を更新するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |
| クラウド プロパティ | デバイス テンプレートを選択すると、テンプレートで定義されているクラウド プロパティごとに、フィールドが追加されます。 |

### <a name="update-device-properties"></a>デバイスのプロパティを更新する

特定のデバイスの書き込み可能なプロパティ値を更新するには、このアクションを使用します。

| フィールド | 説明 |
| ----- | ----------- |
| Application | 一覧から IoT Central アプリケーションを選択します。 |
| Device | 削除するデバイスの一意の ID。 |
| デバイス テンプレート | 一覧から、IoT Central アプリケーションのデバイス テンプレートを選択します。 |
| 書き込み可能なプロパティ | デバイス テンプレートを選択すると、テンプレートで定義されている書き込み可能なプロパティごとに、フィールドが追加されます。 |

## <a name="next-steps"></a>次のステップ

ここでは、Azure IoT Central アプリケーションで高度なルールを作成する方法について学習しました。次は、[Azure IoT Central アプリケーションでデバイス データを分析する](howto-create-analytics.md)方法について説明します