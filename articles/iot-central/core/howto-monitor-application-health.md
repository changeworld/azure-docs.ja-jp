---
title: Azure IoT Central アプリケーションの正常性状態を監視する | Microsoft Docs
description: オペレーターまたは管理者として、IoT Central アプリケーションに接続されているデバイスの全体的な正常性状態を監視します。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 664819b209aeb09093ce8711456b86ff4d3e8949
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84249477"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>IoT Central アプリケーションに接続されているデバイスの全体的な正常性状態を監視する

*この記事は、オペレーターおよび管理者に適用されます。*

この記事では、IoT Central によって提供される一連のメトリックを使用して、IoT Central アプリケーションに接続されているデバイスの全体的な正常性を評価する方法について説明します。

メトリックは、IoT Central アプリケーションでは既定で有効になっており、[Azure portal](https://portal.azure.com/) からアクセスします。 [Azure Monitor データ プラットフォームでは、これらのメトリックが公開](../../azure-monitor/platform/data-platform-metrics.md)されており、これらを対話方式で操作する複数の方法が用意されています。 たとえば、Azure portal、REST API、または PowerShell や Azure CLI のクエリでグラフを使用できます。

### <a name="trial-applications"></a>試用版アプリケーション

無料試用版プランを使用するアプリケーションには、関連付けられた Azure サブスクリプションがないため、Azure Monitor メトリックはサポートされません。 [アプリケーションを標準の料金プランに変換](./howto-view-bill.md#move-from-free-to-standard-pricing-plan)して、これらのメトリックにアクセスすることができます。

## <a name="view-metrics-in-the-azure-portal"></a>Azure portal でメトリックを表示する

次の手順では、[接続されているデバイス](./tutorial-connect-device-nodejs.md)に [IoT Central アプリケーション](./quick-deploy-iot-central.md)があることを前提としています。

ポータルで IoT Central メトリックを表示するには:

1. ポータルで IoT Central アプリケーション リソースに移動します。 既定では、IoT Central リソースは **IOTC** という名前のリソース グループに配置されます。
1. アプリケーションのメトリックからグラフを作成するには、 **[監視]** セクションで **[メトリック]** を選択します。

### <a name="azure-portal-permissions"></a>Azure portal アクセス許可

Azure portal のメトリックへのアクセスは [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)によって管理されます。 Azure portal を使用して、IoT Central アプリケーション/リソース グループ/サブスクリプションにユーザーを追加し、アクセス権を付与します。 ユーザーは、既に IoT Central アプリケーションに追加されている場合でも、ポータルに追加する必要があります。 [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)を使用して、きめ細かなアクセス制御を行うことができます。

## <a name="iot-central-metrics"></a>IoT Central メトリック

次の表では、IoT Central で現在使用できるメトリックについて説明します。

| メトリック | メトリックの表示名 | ユニット | 集計の種類 | 説明 |
|--------|---------------------|------|------------------|-------------|
| connectedDeviceCount         | Total Connected Devices (接続されたデバイスの総数)                              | Count  | 合計             | IoT Central に接続されているデバイスの数                               |
| c2d.property.read.success    | Successful Device Property Reads from IoT Central (IoT Central からのデバイス プロパティ読み取りの成功数)    | Count  | 合計             | IoT Central から開始されて成功した、すべてのプロパティ読み取りの数    |
| c2d.property.read.failure    | Failed Device Property Reads from IoT Central (IoT Central からのデバイス プロパティ読み取りの失敗数)        | Count  | 合計             | IoT Central から開始されて失敗した、すべてのプロパティ読み取りの数        |
| d2c.property.read.success    | Successful Device Property Reads from Devices (デバイスからのデバイス プロパティ読み取りの成功数)        | Count  | 合計             | デバイスから開始されて成功した、すべてのプロパティ読み取りの数        |
| d2c.property.read.failure    | Failed Device Property Reads from Devices (デバイスからのデバイス プロパティ読み取りの失敗数)            | Count  | 合計             | デバイスから開始されて失敗した、すべてのプロパティ読み取りの数            |
| c2d.property.update.success  | Successful Device Property Updates from IoT Central (IoT Central からのデバイス プロパティ更新の成功数)  | Count  | 合計             | IoT Central から開始されて成功した、すべてのプロパティ更新の数  |
| c2d.property.update.failure  | Failed Device Property Updates from IoT Central (IoT Central からのデバイス プロパティ更新の失敗数)      | Count  | 合計             | IoT Central から開始されて失敗した、すべてのプロパティ更新の数      |
| d2c.property.update.success  | Successful Device Property Updates from Devices (デバイスからのデバイス プロパティ更新の成功数)      | Count  | 合計             | デバイスから開始されて成功した、すべてのプロパティ更新の数      |
| d2c.property.update.failure  | Failed Device Property Updates from Devices (デバイスからのデバイス プロパティ更新の失敗数)          | Count  | 合計             | デバイスから開始されて失敗した、すべてのプロパティ更新の数          |

### <a name="metrics-and-invoices"></a>メトリックと請求書

メトリックは、Azure IoT Central の請求書に表示される数値とは異なる場合があります。 こうした状況は、次のようなさまざまな原因によって発生します。

- IoT Central の[標準料金プラン](https://azure.microsoft.com/pricing/details/iot-central/)には、2 つのデバイスとさまざまなメッセージ クォータが無料で含まれています。 無料の項目は課金から除外されますが、メトリックには引き続きカウントされます。

- IoT Central は、アプリケーション内のデバイス テンプレートごとに 1 つのテスト デバイス ID を自動生成します。 このデバイス ID は、デバイス テンプレートの **[テスト デバイスの管理]** ページに表示されます。 ソリューション ビルダーは、これらのテスト デバイス ID を使用するコードを生成することによって、公開する前に[デバイス テンプレートを検証](./overview-iot-central.md#create-device-templates)することができます。 これらのデバイスは課金から除外されますが、メトリックには引き続きカウントされます。

- メトリックはデバイスからクラウドへの通信のサブセットを示す場合がありますが、デバイスとクラウド間のすべての通信は、[課金のメッセージとしてカウントされます](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="next-steps"></a>次のステップ

ここでは、アプリケーション テンプレートの使用方法を学習しました。推奨される次の手順は、[Azure portal で IoT Central を管理する](howto-manage-iot-central-from-portal.md)方法を学習することです。
