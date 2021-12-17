---
title: Azure IoT Hub 用デバイス更新のリソースについて | Microsoft Docs
description: Azure IoT Hub 用デバイス更新のリソースについて
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e8194a269aec09f087632d2f6069d0624d7a835b
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111410659"
---
# <a name="device-update-resources"></a>デバイス更新のリソース

IoT Hub 用デバイス更新を使用するには、デバイス更新アカウントとインスタンス リソースを作成する必要があります。 

## <a name="device-update-account"></a>デバイス更新アカウント

デバイス更新アカウントは、Azure サブスクリプション内に作成されるリソースです。 デバイス更新アカウント レベルでは、自分のデバイス更新アカウントを作成するリージョンを選択できます。 また、デバイス更新にアクセスするユーザーを承認するアクセス許可を設定することもできます。


## <a name="device-update-instance"></a>デバイス更新インスタンス
アカウントが作成されたら、デバイス更新インスタンスを作成する必要があります。 インスタンスとは、特定の IoT ハブに関連付けられている更新プログラムとデプロイを含む論理コンテナーです。 デバイス更新では、デバイス ディレクトリとしての IoT ハブと、デバイスとの通信チャネルが使用されます。 

パブリック プレビュー期間中には、サブスクリプションあたり 2 つのデバイス更新アカウントを作成できます。 また、アカウントごとに 2 つのデバイス更新インスタンスを作成することができます。

## <a name="configuring-device-update-linked-iot-hub"></a>デバイス更新にリンクされた IoT Hub を構成する 

IoT Hub からデバイス更新に変更通知が送られるように、デバイス更新は "組み込みの" イベント ハブと統合されています。 ご利用のインスタンス内の [IoT Hub の構成] ボタンをクリックすると、IoT デバイスと通信するために必要なメッセージ ルート、コンシューマー グループとアクセス ポリシーが構成されます。 

### <a name="message-routing"></a>メッセージのルーティング

デバイス更新用に次のメッセージ ルートが構成されます。

|   ルート名    | データ ソース | ルーティング クエリ  | エンドポイント | 説明  |
| :--------- | :---- |:---- |:---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | DigitalTwinChangeEvents | true | events | デジタル ツインの変更イベントをリッスンします  |
|  DeviceUpdate.DeviceLifecycle | DeviceLifecycleEvents | opType = ' deleteDeviceIdentity ' または opType = ' deleteModuleIdentity '  | events | 削除されたデバイスをリッスンします |
|  DeviceUpdate.DeviceTwinEvents| TwinChangeEvents | (opType = 'updateTwin' OR opType = 'replaceTwin') AND IS_DEFINED($body.tags.ADUGroup) | events | 新しいデバイス更新グループをリッスンします |

> [!NOTE]
> ルート名は、これらのルートを構成するときには実際には問題になりません。 一貫性があり、デバイスの更新に使用されていることを識別しやすい名前にするために、DeviceUpdate をプレフィックスとして含めています。 デバイスの更新が正常に機能するためには、次の表に示すようにルート プロパティの残りの部分を構成する必要があります。 

### <a name="consumer-group"></a>コンシューマー グループ

IoT Hub を構成すると、デバイス Update Management サービスに必要なイベントハブ コンシューマーグループも作成されます。 

:::image type="content" source="media/device-update-resources/consumer-group.png" alt-text="コンシューマー グループのスクリーンショット。" lightbox="media/device-update-resources/consumer-group.png":::

### <a name="access-policy"></a>アクセス ポリシー

デバイスの更新管理サービスで更新可能なデバイスを照会するには、"deviceupdateservice" という名前の共有アクセスポリシーが必要です。 "Deviceupdateservice" ポリシーが作成され、IoT Hub の構成の一部として次のアクセス許可が付与されます。
- レジストリ読み取り
- サービス接続
- デバイス接続

:::image type="content" source="media/device-update-resources/access-policy.png" alt-text="アクセス ポリシーのスクリーンショット。" lightbox="media/device-update-resources/access-policy.png":::

## <a name="next-steps"></a>次のステップ

[デバイス更新リソースを作成する](./create-device-update-account.md)
