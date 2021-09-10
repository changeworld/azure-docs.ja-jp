---
title: Azure IoT Central でのアーキテクチャの概念 | Microsoft Docs
description: この記事では、Azure IoT Central のアーキテクチャに関連する主要な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 37e4224ae1347647d15959a55d19d2c6487935d7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273215"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central のアーキテクチャ

この記事では、Azure IoT Central のアーキテクチャの重要な概念の概要を説明します。

## <a name="devices"></a>デバイス

デバイスは、Azure IoT Central アプリケーションとデータを交換します。 デバイスは次のことを実行できます。

- テレメトリなどの測定を送信します。
- アプリケーションと設定を同期します。

Azure IoT Central では、デバイスがアプリケーションと交換できるデータはデバイス テンプレートで指定されます。 デバイス テンプレートの詳細については、「[デバイス テンプレート](concepts-device-templates.md)」を参照してください。

Azure IoT Central アプリケーションへのデバイスの接続方法の詳細については、[デバイス接続](concepts-get-connected.md)に関するページを参照してください。

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge デバイス

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) を使用して作成されたデバイスと同様に、[Azure IoT Edge デバイス](../../iot-edge/about-iot-edge.md)を IoT Central アプリケーションに接続することもできます。 IoT Edge を使用すると、IoT Central によって管理されている IoT デバイスで、クラウド インテリジェンスやカスタム ロジックを直接実行できます。 IoT Edge をゲートウェイとして使用し、他のダウンストリーム デバイスで IoT Central に接続することもできます。

詳細については、「[Azure IoT Edge デバイスを Azure IoT Central アプリケーションに接続する](concepts-iot-edge.md)」を参照してください。

## <a name="cloud-gateway"></a>クラウド ゲートウェイ

Azure IoT Central は、デバイス接続を可能にするクラウド ゲートウェイとして Azure IoT Hub を使用します。 IoT Hub では、次のことが可能になります。

- クラウド内での大規模なデータ インジェスト。
- デバイスの管理。
- セキュリティ保護されたデバイス接続。

IoT Hub の詳細については、[Azure IoT Hub](../../iot-hub/index.yml) に関するページを参照してください。

Azure IoT Central でのデバイス接続の詳細については、[デバイス接続](concepts-get-connected.md)に関するページを参照してください。

## <a name="data-stores"></a>データ ストア

Azure IoT Central は、クラウド内にアプリケーション データを格納します。 格納されるアプリケーション データには、次のものがあります。

- デバイス テンプレート。
- デバイス ID。
- デバイス メタデータ。
- ユーザーおよびロール データ。

Azure IoT Central は、デバイスから送信された測定データのために時系列ストアを使用します。 分析サービスによって使用されるデバイスからの時系列データ。

## <a name="data-export"></a>データのエクスポート

Azure IoT Central アプリケーションでは、独自の Azure Event Hubs と Azure Service Bus のインスタンスに[データを継続的にエクスポート](howto-export-data.md)できます。 また、データをご自分の Azure BLOB ストレージ アカウントに定期的にエクスポートすることもできます。 IoT Central では、測定、デバイス、デバイス テンプレートをエクスポートできます。

## <a name="batch-device-updates"></a>デバイスの一括更新

Azure IoT Central アプリケーションでは、接続されたデバイスを管理するための[ジョブを作成して実行](howto-manage-devices-in-bulk.md)できます。 これらのジョブを使用すると、デバイスのプロパティや設定を一括更新したり、コマンドを実行したりできます。 たとえば、複数の冷蔵自動販売機のファン速度を上げるジョブを作成できます。

## <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)

すべての IoT Central アプリケーションには、独自の RBAC システムが組み込まれています。 事前定義済みロールの 1 つを使用して、あるいはカスタム ロールを作成することで、Azure IoT Central アプリケーションの[アクセス ルールを管理者は定義できます](howto-manage-users-roles.md)。 ユーザーにアクセスが許可されるアプリケーションの領域と実行できることがロールにより決定されます。

## <a name="security"></a>セキュリティ

Azure IoT Central 内のセキュリティ機能には、次のものがあります。

- データは、転送中および保存時に暗号化されます。
- 認証は、Azure Active Directory または Microsoft アカウントのどちらかによって提供されます。 2 要素認証がサポートされています。
- テナントの完全な分離。
- デバイス レベルのセキュリティ。

## <a name="next-steps"></a>次のステップ

ここでは、Azure IoT Central のアーキテクチャについて学習しました。推奨される次の手順は、Azure IoT Central での[デバイス接続](concepts-get-connected.md)の学習です。