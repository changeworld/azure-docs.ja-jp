---
title: リモート監視ソリューションに SIM データを統合する - Azure| Microsoft Docs
description: この記事では、Telefonica SIM データをリモート監視ソリューションに統合する方法について説明します。
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>SIM データをリモート監視ソリューションに統合する

## <a name="overview"></a>概要
IoT デバイスは、どこからでもデータ ストリームを送信できるようにする SIM カードを使用してクラウドに接続することがよくあります。 Azure IoT リモート監視ソリューションによって SIM 管理データの統合が可能になるため、オペレーターは、SIM が提供するデータによってデバイスの正常性を追跡することもできます。 リモート監視は、追加設定なしで Telefonica IoT との統合を可能にするため、IoT Connectivity Platform を使用しているユーザーはデバイスの SIM 接続データをソリューションと同期できるようになります。 このソリューションは、GitHub リポジトリを介して他の電話会社プロバイダーをサポートするように拡張することができます。
このチュートリアルで学習する内容は次のとおりです。
* SIM データをリモート監視ソリューションに統合する
* テレメトリをリアルタイムで表示する
* SIM データを表示する 

## <a name="telefonica-iot-integration-setup"></a>Telefonica IoT の統合の設定

### <a name="prerequisites"></a>前提条件
接続データを Azure リモート監視ソリューションに同期するには、次の手順に従います。

1.  [Telefonica のサイト](https://iot.telefonica.com/contact)で要求を入力し、連絡先データなど、**[Azure Remote Monitoring]\(Azure リモート監視\)** オプションを選択します。
2.  Telefonica によってアカウントがアクティブ化されます。 
3.  まだ Telefónica クライアントではなく、この IoT Connectivity Cloud Ready サービスまたはその他の IoT Connectivity Cloud Ready サービスを利用したい場合は、[Telefonica のサイト](https://iot.telefonica.com/contact)にアクセスし、**[Connectivity]\(接続性\)** オプションを選択します。

### <a name="telefonica-sim-setup"></a>Telefonica SIM の設定
Telefónica SIM と Azure Twin デバイス ID の関連付けは、Telefónica IoT SIM の "alias" プロパティに基づきます。 

[Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/)、[SIM Inventory]\(SIM インベントリ\) の順に移動し、SIM を選択して、各 SIM の "alias" を目的の Twin デバイス ID で更新します。 

このタスクは、一括モードで実行することもできます (Telefónica IoT Connectivity Platform のユーザー マニュアルを参照してください)

![Telefonica の更新](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

デバイスをリモート監視に接続するには、[C](iot-suite-connecting-devices-linux.md) または [Node](iot-suite-connecting-devices-node.md) を使用したこれらのチュートリアルに従います。 

## <a name="view-device-telemetry-and-sim-properties"></a>デバイス テレメトリと SIM プロパティの表示
Telefonica アカウントが正しく構成され、デバイスが接続されると、デバイスの詳細と SIM データを表示できます。
次の接続パラメーターを公開できます。
* ICCID
* IP
* ネットワークの有無
* SIM の状態
* ネットワークベースの場所
* 消費されたデータ トラフィック

![ダッシュボード](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>次の手順

Azure IoT リモート監視に SIM データを統合する方法の概要を確認できたので、ソリューション アクセラレータに推奨される次の手順を以下に示します。

* [Azure IoT リモート監視ソリューションの操作](iot-suite-remote-monitoring-explore.md)
* [高度な監視の実行](iot-suite-remote-monitoring-monitor.md)
* [デバイスの管理](iot-suite-remote-monitoring-manage.md)
* [デバイスの問題のトラブルシューティング](iot-suite-remote-monitoring-maintain.md)

