---
title: Swift での粗い再局在化
description: Swift でデバイス上のセンサーを使用してアンカーを作成して配置する方法の詳細な説明。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 1381f6340d1b85376ad5be9e00c596aa921d957f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277175"
---
# <a name="how-to-create-and-locate-anchors-using-on-device-sensors-in-swift"></a>Swift でデバイス上のセンサーを使用してアンカーを作成して配置する方法

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure Spatial Anchors では、デバイス上の配置センサー データを、作成したアンカーに関連付けることができます。 このデータを使用して、デバイスの近くにアンカーがあるかどうかをすばやく判断することもできます。 詳細については、「[粗い再局在化](../concepts/coarse-reloc.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このガイドを完了するには、以下が必要です。

- Swift の基本的な知識。
- 「[Azure Spatial Anchors の概要](../overview.md)」を読んでいる。
- [5 分間のクイック スタート](../index.yml)のいずれかを完了している。
- [アンカーを作成して配置する方法](../create-locate-anchors-overview.md)に関するページを読んでいる。

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Allow GPS
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true

// Allow WiFi scanning
sensors.wifiEnabled = true

// Populate the set of known BLE beacons' UUIDs
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

// Allow the set of known BLE beacons
sensors.bluetoothEnabled = true
sensors.knownBeaconProximityUuids = uuids
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```swift
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider

// Configure the near-device criteria
let nearDeviceCriteria = ASANearDeviceCriteria()!
nearDeviceCriteria.distanceInMeters = 5.0
nearDeviceCriteria.maxResultCount = 25

// Set the session's locate criteria
let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]