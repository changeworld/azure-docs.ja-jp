---
title: インクルード ファイル
description: インクルード ファイル
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 5702c6e9c9d75c6cccb82f1c57684ef7b9898c34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666011"
---
## <a name="view-device-telemetry"></a>デバイス テレメトリを表示する

デバイスから送信されたテレメトリは、ソリューションの **[デバイス]** ページで表示できます。

1. プロビジョニングしたデバイスを、**[デバイス]** ページのデバイスの一覧から選択します。 パネルには、デバイス テレメトリのプロットなど、デバイスに関する情報が表示されます。

    ![デバイスの詳細を確認する](media/iot-suite-visualize-connecting/devicesdetail.png)

1. **[気圧]** を選択して、テレメトリの表示を変更します。

    ![気圧テレメトリを表示する](media/iot-suite-visualize-connecting/devicespressure.png)

1. デバイスに関する診断情報を表示するには、下へスクロールして、**[診断]** に移動します。

    ![デバイスの診断を表示する](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>デバイスを操作する

デバイスでメソッドを呼び出すには、リモート監視ソリューションの **[デバイス]** ページを使用します。 たとえば、リモート監視ソリューションで、**Chiller** デバイスが **FirmwareUpdate** メソッドを実装しています。

1. **[デバイス]** を選択して、ソリューションの **[デバイス]** ページに移動します。

1. プロビジョニングしたデバイスを、**[デバイス]** ページのデバイスの一覧から選択します。

    ![物理デバイスを選択する](media/iot-suite-visualize-connecting/devicesselect.png)

1. デバイスで呼び出すことができるメソッドの一覧を表示するには、**[ジョブ]** を選択してから、**[Run メソッド]** を選択します。 複数のデバイスで実行するジョブのスケジュールを設定するために、一覧から複数のデバイスを選択することができます。 **[ジョブ]** パネルには、選択したすべてのデバイスに共通のメソッドの型が表示されます。

1. **[FirmwareUpdate]** を選択し、ジョブ名を **UpdatePhysicalChiller** に設定します。 **[ファームウェア バージョン]** を **[2.0.0]** に、**[Firmware URI]\(ファームウェアの URI\)** を **http://contoso.com/updates/firmware.bin** に設定し、**[適用]** を選択します。

    ![ファームウェア更新のスケジュール](media/iot-suite-visualize-connecting/deviceschedule.png)

1. シミュレートされたデバイスがメソッドを処理しているとき、デバイス コードを実行しているコンソールに一連のメッセージが表示されます。

1. 更新が完了すると、新しいバージョンのファームウェアが **[デバイス]** ページに表示されます。

    ![更新の完了](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> ソリューションでジョブの状態を追跡するには、**[表示]** を選択します。

## <a name="next-steps"></a>次の手順

[「リモート監視の構成済みソリューションのカスタマイズ](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md)」の記事では、ソリューション アクセラレータをカスタマイズする方法をいくつか説明します。