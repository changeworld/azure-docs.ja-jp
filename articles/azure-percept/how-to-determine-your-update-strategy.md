---
title: Azure Percept DK の更新方法を決定する
description: Azure Percept DK の OTA または USB ケーブル経由の更新の長所と短所。 さまざまなユーザーが最適な更新方法を選択するための推奨事項。
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: d5b3f793f6fdf7fe1182c700da8635df9b2ef9a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820547"
---
# <a name="determine-your-update-strategy-for-azure-percept-dk"></a>Azure Percept DK の更新方法を決定する

Microsoft では、Azure Percept DK ソフトウェアを最新の状態に保つために、開発キットの更新方法を 2 種類提供しています。 **USB ケーブル経由の更新** と **無線 (OTA) 更新** です。

USB ケーブル経由の更新では、開発キットを対象としたクリーン インストールが実行されます。 新しいイメージがデプロイされた後、既存の構成と各パーティション内のすべてのユーザー データが消去されます。 これを行うには、Type-C の USB ケーブルを使用して開発キットをホスト システムに接続します。 ホスト システムには、Windows または Linux マシンを使用できます。  この更新方法は、出荷時の設定にリセットする方法として使用することもできます。 これを行うには、まったく同じバージョンを開発キットに再デプロイします。 USB ケーブル経由の更新の詳細については、「[USB-C ケーブル接続で Azure Percept DK を更新する](./how-to-update-via-usb.md)」を参照してください。

OTA 更新は、[Device Update for IoT Hub](../iot-hub-device-update/device-update-resources.md) Azure サービスの上に構築されています。 この種類の更新を行うには、開発キットを Azure IoT Hub に接続します。 構成とユーザー データは、OTA 更新後も保持されます。 OTA 更新の実行の詳細については、[無線 (OTA) による Azure Percept DK の更新](./how-to-update-over-the-air.md)に関するページを参照してください。

USB ケーブル経由の更新と OTA 更新の両方の長所と短所を確認したうえで、さまざまなシナリオに応じた Microsoft の推奨事項に従ってください。

## <a name="usb-cable-update"></a>USB ケーブル経由の更新

- 長所
  - 開発キットをインターネットまたは Azure に接続する必要がありません。
  - 開発キットに現在読み込まれているソフトウェアとファームウェアのバージョンに関係なく、常に最新のイメージを適用できます。
- 短所
  - デバイスを再イメージ化します。構成とユーザー データは削除されます。
  - OOBE をやり直すと共に、事前に読み込まれていないコンテナーをダウンロードする必要があります。
  - リモートで実行することはできません。

## <a name="ota-update"></a>OTA 更新

- 長所
  - ユーザー データ、構成、およびダウンロードされたコンテナーが保持されます。 開発キットが、OTA 後も引き続き動作します。
  - 更新をリモートで実行できます。
  - 複数の類似デバイスを同時に更新することができます。 また、たとえば夜間に更新が実行されるように更新のスケジュールを設定することもできます。
- 短所
  - スキップすることができないハードストップ バージョンがある可能性があります。 「[OTA のハードストップ バージョン](./software-releases-over-the-air-updates.md#hard-stop-version-of-ota)」を参照してください。
  - "Device Update for IoT Hub" 機能が適切に構成されている IoT Hub にデバイスを接続する必要があります。
  - ダウングレードにはうまく動作しません。

> [!IMPORTANT]
> Device Update for IoT Hub では、現在実行されている OS よりも古いバージョンのイメージのデプロイがブロックされません。 ただし、開発キットに対してこれを行うと、データと機能が失われます。

## <a name="microsoft-recommendations"></a>Microsoft の推奨事項

|型|シナリオ|Update メソッド|
|:---:|---|:---:|
|運用|既に開発キットでソリューションを実行している状態、または開発キットをフィールドにデプロイしている状態で、最新の修正プログラムとセキュリティ更新プログラムを利用するために開発キットを最新の状態に保つ。|OTA|
|運用または開発|新しい開発キットを開梱し、最新のソフトウェアに更新する。|USB|
|運用または開発|既にいくつかの月次リリースをスキップしている状態で、最新のソフトウェア バージョンに更新する。|USB|
|運用または開発|開発キットを出荷時の設定にリセットする。|USB|
|開発|ソリューションの開発中に開発キットの OS と F/W を最新の状態に保つ。|USB または OTA|
|開発|問題の調査またはデバッグのために、特定の (古い) バージョンにジャンプする。|USB|

## <a name="next-steps"></a>次の手順

更新方法を決定したら、次のページにアクセスして更新を準備します。

USB ケーブル経由の更新

- [USB-C ケーブル接続で Azure Percept DK を更新する](./how-to-update-via-usb.md)
- [USB ケーブル経由の更新のための Azure Percept DK ソフトウェア リリース](./software-releases-usb-cable-updates.md)

OTA

- [Azure Percept DK を無線 (OTA) で更新する](./how-to-update-over-the-air.md)
- [OTA 更新のための Azure Percept DK ソフトウェア リリース](./software-releases-over-the-air-updates.md)