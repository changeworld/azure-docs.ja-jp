---
title: "Azure Portal を使用してファイルのアップロードを構成する | Microsoft Docs"
description: "Azure Portal を使用してファイルのアップロードを構成する方法の概要"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e3ac3e8cee2724b76f51423d1a6757382cca04f0


---
# <a name="configure-file-uploads-using-the-azure-portal"></a>Azure Portal を使用してファイルのアップロードを構成する
## <a name="file-upload"></a>ファイルのアップロード
[IoT Hub でファイルのアップロード機能][lnk-upload]を使用するには、最初に Azure Storage アカウントとハブを関連付ける必要があります。 **[ファイルのアップロード]** 設定を選択して、変更対象の IoT Hub のファイル アップロード プロパティのリストを表示します。

![][13]

**ストレージ コンテナー**: Azure Portal を使用して現在の Azure サブスクリプションの Azure Storage アカウントの BLOB コンテナーを選択し、IoT Hub に関連付けます。 必要に応じて、**[ストレージ アカウント]** ブレードで Azure Storage アカウントを作成し、**[コンテナー]** ブレードで BLOB コンテナーを作成できます。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

![][14]

**Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)**: トグル ボタンを使用して、ファイルのアップロードに関する通知を有効または無効にします。

**SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

**File notification settings default TTL (ファイルの通知設定 既定の TTL)**: 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定で 1 時間に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

**File notification maximum delivery count (ファイルの通知設定 最大配信回数)**: IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定で 10 に設定されますが、スライダーを使用して、他の値にカスタマイズできます。

![][15]

## <a name="next-steps"></a>次のステップ
IoT Hub のファイルのアップロード機能に関する詳細については、開発者ガイドの「[Upload files from a device][lnk-upload]」(デバイスからのファイルのアップロード) をご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理][lnk-bulk]
* [使用状況メトリック][lnk-metrics]
* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Nov16_HO3-->


