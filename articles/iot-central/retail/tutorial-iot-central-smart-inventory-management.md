---
title: チュートリアル - Azure IoT スマート インベントリの管理 | Microsoft Docs
description: このチュートリアルでは、IoT Central のスマート インベントリの管理アプリケーション テンプレートをデプロイして使用する方法について説明します
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 747bcaffd1e24937580dcf95f352a34f66c3314e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437510"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-inventory-management-application-template"></a>チュートリアル: スマート インベントリの管理アプリケーション テンプレートをデプロイして調べる

IoT Central の "*スマート インベントリの管理*" アプリケーション テンプレートとこの記事のガイダンスを使用して、エンドツーエンドのスマート インベントリの管理ソリューションを開発します。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-architecture.png" alt-text="スマート インベントリの管理。":::

1. ゲートウェイ デバイスにテレメトリ データを送信している IoT センサーのセット
2. テレメトリと集計された分析情報を IoT Central に送信しているゲートウェイ デバイス
3. データは、操作のために目的の Azure サービスにルーティングされます
4. ASA や Azure Functions などの Azure サービスを使用して、データ ストリームの形式を再設定し、目的のストレージ アカウントに送信できます 
5. 処理されたデータは、ほぼリアルタイムのアクションのためにホット ストレージに格納されるか、ML やバッチ分析に基づいて分析情報をさらに拡充するためにコールド ストレージに格納されます。 
6. Logic Apps を使用すると、エンド ユーザーのビジネス アプリケーションでさまざまなビジネス ワークフローを利用できます

### <a name="details"></a>詳細

次のセクションでは、無線自動識別 (RFID) タグと Bluetooth Low Energy (BLE) タグからのテレメトリ インジェストの概念アーキテクチャの各部分の概要を説明します

### <a name="rfid-tags"></a>RFID タグ

RFID タグは、アイテムに関するデータを無線で送信します。 通常、RFID タグは、指定されている場合を除き、バッテリーを持っていません。 タグは、リーダーによって生成された無線波からエネルギーを受け取り、RFID リーダーに対して信号を送り返します。

### <a name="ble-tags"></a>BLE タグ

エネルギー ビーコンは、一定の間隔でデータのパケットをブロードキャストします。 ビーコン データは、BLE リーダーまたはスマートフォンにインストールされたサービスによって検出され、クラウドに転送されます。

### <a name="rfid--ble-readers"></a>RFID および BLE リーダー

RFID リーダーは、無線波をより使いやすい形式のデータに変換します。 タグから収集された情報は、ローカル エッジ サーバーに格納されるか、JSON-RPC 2.0 over MQTT を使用してクラウドに送信されます。
アクセス ポイント (AP) とも呼ばれる BLE リーダーは、RFID リーダーに似ています。 これにより、近くの Bluetooth 信号が検出され、そのメッセージが JSON-RPC 2.0 over MQTT を使用してローカル Azure IoT Edge またはクラウドに中継されされます。
多くのリーダーは、RFID およびビーコン信号を読み取ることができ、温度、湿度、加速度計、およびジャイロスコープに関連する追加のセンサー機能を提供します。

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge ゲートウェイ

Azure IoT Edge サーバーを使うと、クラウドに送信される前のデータをローカルに前処理できます。 また、クラウド ワークロードの人工知能、Azure およびサードパーティのサービス、ビジネス ロジックを、標準のコンテナーを使用してデプロイすることもできます。

### <a name="device-management-with-iot-central"></a>IoT Central によるデバイス管理 

Azure IoT Central はソリューション開発プラットフォームであり、IoT デバイスの接続、構成、管理が簡単になります。 このプラットフォームを使うと、IoT デバイスの管理、運用、関連開発の負担とコストが大幅に削減されます。 顧客とパートナーは、エンドツーエンドのエンタープライズ ソリューションを構築し、インベントリ管理でデジタル フィードバック ループを実現できます。

### <a name="business-insights--actions-using-data-egress"></a>データ エグレスを使用したビジネスの分析情報とアクション 

IoT Central プラットフォームでは、継続的データ エクスポート (CDE) と API を通じて豊富な機能拡張オプションが提供されます。 一般に、テレメトリ データ処理または未加工のテレメトリに基づくビジネス分析情報は、優先される基幹業務アプリケーションにエクスポートされます。 Webhook、サービス バス、イベント ハブ、または BLOB ストレージを使用して、機械学習モデルを構築、トレーニング、デプロイし、分析情報をさらに補強することによって、これを実現できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * スマート インベントリの管理アプリケーションを作成する 
> * アプリケーションを調べる 

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な特定の前提条件はありません
* Azure サブスクリプションを用意することをお勧めしますが、なくても試してみることはできます

## <a name="create-smart-inventory-management-application"></a>スマート インベントリの管理アプリケーションを作成する

次の手順に従ってアプリケーションを作成します。

1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[小売業]** タブを選択します。:::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc-retail-home-page.png" alt-text="スマート インベントリの管理アプリケーション テンプレートからアプリの作成方法を示すスクリーンショット":::

1. **[スマート インベントリの管理]** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboard"></a>ダッシュボード 

アプリ テンプレートが正常にデプロイされると、既定のダッシュボードがスマート インベントリの管理のオペレーターを対象とするポータルになります。 Northwind Traders 社は、Bluetooth Low Energy (BLE) による倉庫管理と無線自動識別 (RFID) による小売店管理を行っている架空のスマート インベントリ プロバイダーです。 このダッシュボードには、インベントリに関するテレメトリを提供する 2 つの異なるゲートウェイと、関連するコマンド、ジョブ、アクションが表示されます。 このダッシュボードは、スマート インベントリ管理の重要なデバイス操作アクティビティが表示されるように事前に構成されています。
ダッシュボードは、2 つの異なるゲートウェイ デバイス管理操作に論理的に分割されています。 
   * 倉庫については、大きな施設のインベントリを追跡およびトレースするために、固定の BLE ゲートウェイと BLE タグがパレットにデプロイされています
   * 小売店については、店舗の在庫を追跡およびトレースするために、個々のアイテム レベルで固定の RFID ゲートウェイと RFID タグが実装されています
  * ゲートウェイの[場所](../core/howto-use-location-data.md)、状態、関連する詳細を表示します 

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-1.png" alt-text="スマート インベントリの管理ダッシュボード (上半分) のスクリーンショット。":::

    * ゲートウェイ、アクティブなタグ、不明なタグの合計数を簡単に追跡できます。
    * ファームウェアの更新、センサーの無効化、センサーの有効化、センサーしきい値の更新、テレメトリ間隔の更新、デバイス サービス コントラクトの更新などのデバイス管理操作を実行できます
    * ゲートウェイ デバイスでは、完全スキャンまたは増分スキャンを使用してオンデマンドのインベントリ管理を実行できます。

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-2.png" alt-text="スマート インベントリの管理ダッシュボード (下半分) のスクリーンショット。":::

### <a name="device-template"></a>デバイス テンプレート

[デバイス テンプレート] タブをクリックすると、ゲートウェイ機能モデルが表示されます。 機能モデルは、 **[Gateway Telemetry & Property]\(ゲートウェイ テレメトリとプロパティ\)** と **[Gateway Commands]\(ゲートウェイ コマンド\)** という 2 つの異なるインターフェイスを中心に構成されています

**[ゲートウェイのテレメトリとプロパティ]** - このインターフェイスには、センサーに関連するすべてのテレメトリ、場所、デバイス情報と、ゲートウェイのしきい値や更新間隔などのデバイス ツイン プロパティ機能が表示されます。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-1.png" alt-text="アプリケーションのインベントリ ゲートウェイ デバイス テンプレートを表示する画面のスクリーンショット。":::

**[Gateway Commands]\(ゲートウェイ コマンド\)** - このインターフェイスには、すべてのゲートウェイ コマンド機能が整理されています

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-2.png" alt-text="インベントリ ゲートウェイ デバイス テンプレートのゲートウェイ コマンド インターフェイスを表示する画面のスクリーンショット。":::

### <a name="rules"></a>ルール

[Rules]\(規則\) タブを選択して、このアプリケーション テンプレートに存在する 2 つの異なる規則を表示します。 これらの規則は、詳細な調査のためオペレーターにメールで通知するように構成されています。

**[Gateway offline]\(ゲートウェイがオフライン\)** : この規則は、長期間にわたってゲートウェイからクラウドへのレポートがない場合にトリガーされます。 バッテリ低下モード、接続喪失、デバイス正常性といった原因により、ゲートウェイが応答しないことがあります。

**[Unknown tags]\(不明なタグ\)** : 資産に関連付けられているすべての RFID タグと BLE タグを追跡することが重要です。 ゲートウェイで検出される不明タグの数が多すぎる場合は、タグ ソーシング アプリケーションとの同期に問題があることを示します。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-rules.png" alt-text="スマート インベントリの管理アプリケーションの規則一覧を示すスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションをもう使わない場合は、 **[管理]**  >  **[アプリケーションの設定]** に移動し、 **[削除]** をクリックすることによって、アプリケーション テンプレートを削除します。

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-cleanup.png" alt-text="使い終えたアプリケーションを削除する方法を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

スマート在庫管理についてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [スマート在庫管理の概念](./architecture-smart-inventory-management.md)
