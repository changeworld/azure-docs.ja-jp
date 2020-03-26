---
title: IoT コネクテッド ロジスティクスのチュートリアル | Microsoft Docs
description: IoT Central 用のコネクテッド ロジスティクス アプリケーション テンプレートのチュートリアル
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d7050cfdae2c726a3a5bd44b4399792a400572a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025522"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>チュートリアル:コネクテッド ロジスティクス アプリケーション テンプレートをデプロイして調べる



このチュートリアルでは、IoT Central の**コネクテッド ロジスティクス** アプリケーション テンプレートをデプロイする方法について説明します。 テンプレートをデプロイする方法、既定で含まれるもの、次にできることについて説明します。

このチュートリアルでは、次の内容を学習します。

* コネクテッド ロジスティクス アプリケーションを作成します
* アプリケーションを調べる 

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な特定の前提条件はありません
* Azure サブスクリプションを用意することをお勧めしますが、なくても試してみることはできます

## <a name="create-connected-logistics-application-template"></a>コネクテッド ロジスティクス アプリケーション テンプレートを作成する

以下の手順でアプリケーションを作成できます

1. Azure IoT Central のアプリケーション マネージャー Web サイトに移動します。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[Retail]\(小売り\)** タブをクリックします。

    > [!div class="mx-imgBorder"]
    > ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. **[Connected Logistics Application]\(コネクテッド ロジスティクス アプリケーション\)** の **[アプリの作成]** を選択します

3. **[アプリの作成]** を選択すると、[新しいアプリケーション] フォームが開き、次のように要求された詳細が設定されます。
   * **[アプリケーション名]** : 既定の推奨名を使用するか、わかりやすいアプリケーション名を入力できます。
   * **[URL]** : 既定の推奨 URL を使用するか、わかりやすい一意の URL を入力できます。 次に、Azure サブスクリプションが既にある場合は、既定の設定をお勧めします。 7 日間の無料試用版料金プランから始め、無料試用版が期限切れになる前に、いつでも標準の料金プランに変換することもできます。
   * **課金情報**:リソースをプロビジョニングするには、ディレクトリ、Azure サブスクリプション、リージョンの詳細が必要です。
   * **作成**:ページの下部にある [作成] を選択して、アプリケーションをデプロイします。

    > [!div class="mx-imgBorder"]
    > ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![コネクテッド ロジスティクスの課金情報](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>アプリケーションを調べる 

## <a name="dashboard"></a>ダッシュボード

アプリ テンプレートが正常にデプロイされると、既定のダッシュボードがコネクテッド ロジスティクスのオペレーターを対象とするポータルになります。 Northwind Traders 社は、海上と陸上の貨物を管理する架空のロジスティクス プロバイダーです。 このダッシュボードには、出荷に関するテレメトリを提供する 2 つの異なるゲートウェイと、実行可能な関連コマンド、ジョブ、アクションが表示されます。 このダッシュボードは、重要なロジスティクス デバイス操作アクティビティが表示されるように事前に構成されています。
ダッシュボードは、2 つの異なるゲートウェイ デバイス管理操作に論理的に分割されています。 
   * トラック輸送の物流経路と海上輸送の場所の詳細は、すべてのマルチモーダル輸送に不可欠な要素です
   * ゲートウェイの状態と関連情報を表示します 

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * ゲートウェイ、アクティブなタグ、不明なタグの合計数を簡単に追跡できます。
   * ファームウェアの更新、センサーの無効化、センサーの有効化、センサーしきい値の更新、テレメトリ間隔の更新、デバイス サービス コントラクトの更新などのデバイス管理操作を実行できます。
   * デバイスのバッテリ消費量を表示します

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>デバイス テンプレート

[デバイス テンプレート] タブをクリックすると、ゲートウェイ機能モデルが表示されます。 機能モデルは、 **[Gateway Telemetry & Property]\(ゲートウェイ テレメトリとプロパティ\)** と **[Gateway Commands]\(ゲートウェイ コマンド\)** という 2 つの異なるインターフェイスを中心に構成されています

**[ゲートウェイのテレメトリとプロパティ]** - このインターフェイスには、センサーに関連するすべてのテレメトリ、場所、デバイス情報と、センサーのしきい値や更新間隔などのデバイス ツイン プロパティ機能が表示されます。

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**[Gateway Commands]\(ゲートウェイ コマンド\)** - このインターフェイスには、すべてのゲートウェイ コマンド機能が整理されています

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>ルール
[Rules]\(規則\) タブを選択して、このアプリケーション テンプレートに存在する 2 つの異なる規則を表示します。 これらの規則は、詳細な調査のためオペレーターにメールで通知するように構成されています。
 
**[Gateway theft alert]\(ゲートウェイ盗難アラート\)** : この規則は、輸送中にセンサーによって予期しない光が検出された場合にトリガーされます。 オペレーターは、潜在的な盗難を調査するためにすぐに通知を受け取る必要があります。
 
**[Unresponsive Gateway]\(応答しないゲートウェイ\)** : この規則は、長期間にわたってゲートウェイからクラウドへのレポートがない場合にトリガーされます。 バッテリ低下モード、接続喪失、デバイス正常性といった原因により、ゲートウェイが応答しないことがあります。

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>ジョブ
[ジョブ] タブを選択すると、このアプリケーション テンプレートの一部として存在する 5 つの異なるジョブが表示されます。

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

ジョブ機能を使用して、ソリューション全体の操作を実行できます。 ジョブでは、デバイス コマンドとツイン機能を使用して、すべてのゲートウェイでの特定のセンサーの無効化や、出荷モードとルートに応じたセンサーのしきい値の変更などのタスクが実行されます。 
   * これは、海上輸送中にバッテリーを節約するために感電センサーを無効にしたり、コールド チェーン輸送中に温度しきい値を下げたりする標準的な操作です。 
 
   * ジョブを使うと、ゲートウェイでのファームウェアの更新やサービス コントラクトの更新など、システム全体の操作を実行して、メンテナンス アクティビティを最新の状態に保つことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このアプリケーションをもう使わない場合は、 **[管理]**  >  **[アプリケーションの設定]** に移動し、 **[削除]** をクリックすることによって、アプリケーション テンプレートを削除します。

> [!div class="mx-imgBorder"]
> ![コネクテッド ロジスティクスのダッシュボード](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>次のステップ
* [コネクテッド ロジスティクスの概念](./architecture-connected-logistics-pnp.md)についてさらに詳しく学習します
* 他の [IoT Central 小売りテンプレート](./overview-iot-central-retail-pnp.md)についてさらに詳しく学習します
* [IoT Central の概要](../core/overview-iot-central.md)についてさらに詳しく学習します
