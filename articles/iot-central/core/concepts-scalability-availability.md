---
title: Azure IoT Central のスケーラビリティと高可用性 | Microsoft Docs
description: この記事では、より多くのデバイスとその高可用性を処理するために IoT Central で自動的にスケーリングする方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: bea4e5d7dcb4349f17a4a967d8e0ef15e6f737b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092279"
---
# <a name="scalability-and-high-availability"></a>スケーラビリティと高可用性

IoT Central アプリケーションは、Azure IoT Central や Device Provisioning Service (DPS) などの複数の Azure サービスを内部的に使用します。 これらの基になるサービスの多くは、複数のテナントに対応しています。 ただし、顧客データの完全な分離を確保するために、IoT Central はシングルテナントの IoT ハブを使用します。 IoT Central は、基になるサービスの複数のインスタンスを自動的に管理し、IoT Central アプリケーションを拡張して高可用性を実現します。

IoT Central は、アプリケーションの読み込みプロファイルに基づいて IoT ハブを自動的にスケーリングします。 IoT Central は、個々の IoT ハブをスケールアップし、IoT ハブの数をスケールアウトできます。 高可用性デバイス接続の場合、すべての IoT Central に少なくとも 2 つの IoT Hub が常に存在します。 IoT Central は IoT ハブを管理しますが、複数の IoT ハブを使用すると、デバイスのファームウェアの実装に影響します。

IoT Central アプリケーション内の IoT ハブはすべて同じ Azure リージョンに存在します。 そのため、複数の IoT ハブ アーキテクチャは、分離された障害が発生した場合に、高可用性デバイス接続を提供します。 Azure リージョン全体が使用できなくなった場合、ディザスター リカバリーの手順では、IoT Central アプリケーション全体を別のリージョンにフェールオーバーします。

## <a name="device-provisioning"></a>デバイス プロビジョニング

デバイスを IoT Central に接続する前に、デバイスを登録し、基になるサービスにプロビジョニングする必要があります。 IoT Central アプリケーションにデバイスを追加すると、IoT Central DPS 登録グループにエントリが追加されます。 ID スコープ、デバイス ID、キーなどの登録グループの情報は、IoT Central UI に表示されます。

デバイスが初めて IoT Central アプリケーションに接続すると、DPS は、登録グループのリンクされた IoT ハブのいずれかにデバイスをプロビジョニングします。 DPS は、割り当てポリシーを使用して、アプリケーションの IoT ハブ間でプロビジョニングの負荷を分散します。 このプロセスでは、各 IoT ハブのプロビジョニングされたデバイスの数が同じであることを確認します。

IoT Central での登録とプロビジョニングの詳細については、[Azure IoT Central に接続する方法](concepts-get-connected.md)に関するページを参照してください。

## <a name="device-connections"></a>デバイスの接続

DPS がデバイスを IoT ハブにプロビジョニングすると、デバイスは常にそのハブに接続しようとします。 デバイスがプロビジョニングされている IoT ハブに接続できない場合は、IoT Central アプリケーションに接続できません。 このシナリオに対処するには、デバイスのファームウェアに再試行戦略を含める必要があります。

デバイスのファームウェアで接続エラーを処理し、別のハブに接続する方法の詳細については、「[デバイス開発のベスト プラクティス](concepts-best-practices.md)」を参照してください。

デバイスのファームウェアが接続エラーを処理できるかどうかを確認する方法の詳細については、「[テスト フェールオーバー機能](concepts-best-practices.md#test-failover-capabilities)」を参照してください。

## <a name="data-export"></a>データのエクスポート

IoT Central アプリケーションは、多くの場合、他のユーザーが構成したサービスを使用します。 たとえば、Azure Event Hubs や Azure Blob Storage などのサービスに継続的にデータをエクスポートするように IoT Central アプリケーションを構成できます。

構成されたデータのエクスポートが変換先に書き込むことができない場合、IoT Central は最大 15 分間データの再転送を試行します。その後、IoT Central によって宛先が失敗としてマークされます。 失敗した宛先は、書き込み可能かどうかを確認するために定期的にチェックされます。

データのエクスポートを無効にしてから再度有効にすることで、失敗したエクスポートを再起動するように IoT Central に強制できます。

使用しているデータ エクスポート先サービスの高可用性とスケーラビリティのベスト プラクティスを確認します。

- Azure Blob Storage: 「[Azure Storage の冗長性](../../storage/common/storage-redundancy.md)」と「[BLOB ストレージのパフォーマンスとスケーラビリティのチェックリスト](../../storage/blobs/storage-performance-checklist.md)」
- Azure Event Hubs: 「[Azure Event Hubs での可用性と一貫性](../../event-hubs/event-hubs-availability-and-consistency.md)」と「[Event Hubs によるスケーリング](../../event-hubs/event-hubs-scalability.md)」
- Azure Service Bus: 「[Service Bus の障害および災害に対するアプリケーションの保護のベスト プラクティス](../../service-bus-messaging/service-bus-outages-disasters.md)」と「[Azure Service Bus 名前空間のメッセージング ユニットを自動的に更新する](../../service-bus-messaging/automate-update-messaging-units.md)」

## <a name="limitations"></a>制限事項

現時点では、2021 年 4 月より前に作成されたレガシ IoT Central アプリケーションはいくつかありますが、まだ複数の IoT ハブ アーキテクチャに移行されていません。 `az iot central device manual-failover` コマンドを使用して、アプリケーションが 1 つの IoT ハブをまだ使用しているかどうかを確認します。

現時点では、IoT Edge デバイスを IoT ハブ間で移動することはできません。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central のスケーラビリティと高可用性について学習しました。推奨される次の手順は、Azure IoT Central での[デバイス接続](concepts-get-connected.md)の学習です。
