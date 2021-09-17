---
title: チュートリアル - Azure IoT スマート メーター監視 | Microsoft Docs
description: このチュートリアルでは、IoT Central のスマート メーター監視アプリケーション テンプレートをデプロイして使用する方法について説明します。
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a332ab10ce4e7c38442288165c56d1161081cd9c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179483"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-meter-monitoring-app-template"></a>チュートリアル:スマート メーター監視アプリ テンプレートをデプロイして利用する 

IoT Central の "*スマート メーター監視*" アプリケーション テンプレートとこの記事のガイダンスを使用して、エンドツーエンドのスマート メーター監視ソリューションを開発します。

  :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-app-architecture.png" alt-text="スマート メーターのアーキテクチャ。":::

このアーキテクチャは、次のコンポーネントで構成されます。 一部のソリューションでは、ここに記載されているすべてのコンポーネントが必要ではない場合があります。

### <a name="smart-meters-and-connectivity"></a>スマート メーターと接続性

スマート メーターは、すべてのエネルギー アセットの中でも最も重要なデバイスの 1 つです。 これを使ってエネルギー消費データを記録し、監視やその他のユース ケース (請求書や需要応答など) のために公共事業体に伝達できます。 メーターの種類に基づいて、ゲートウェイ、またはその他の中間デバイスや中間システム (エッジ デバイス、ヘッドエンド システムなど) を使用して IoT Central に接続できます。 直接接続できないデバイスを接続するには、IoT Central デバイス ブリッジを構築します。 IoT Central デバイス ブリッジはオープン ソース ソリューションであり、詳細については[こちら](../core/howto-build-iotc-device-bridge.md)で確認できます。 

### <a name="iot-central-platform"></a>IoT Central プラットフォーム

Azure IoT Central は、IoT ソリューションの構築を簡素化し、IoT の管理、運用、開発の負担とコストを削減するために役立つプラットフォームです。 IoT Central を使用すると、大規模なモノのインターネット (IoT) アセットを簡単に接続、監視、管理できます。 スマート メーターを IoT Central に接続すると、アプリ テンプレートにはデバイス モデル、コマンド、ダッシュボードなどの組み込みの機能が使用されます。 また、アプリ テンプレートには、ほぼリアルタイムのメーター データの監視、分析、ルール、視覚化など、ウォーム パス シナリオにも IoT Central ストレージが使用されます。 

### <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central を使用して構築する拡張オプション

IoT Central プラットフォームには、次の 2 つの拡張オプションがあります。継続的データ エクスポート (CDE) と API。 顧客とパートナーは、特定のニーズに合わせてこれらのオプションのいずれかを選択し、ソリューションをカスタマイズすることができます。 たとえば、あるパートナーは Azure Data Lake Storage (ADLS) を使用して CDE を構成しました。 バッチ処理、監査、報告などの目的で、長期的なデータ保持やその他のコールド パス ストレージ シナリオに ADLS が使用されています。

このチュートリアルでは、次の作業を行う方法について説明します。

- スマート メーター アプリを無料で作成する
- アプリケーションのチュートリアル
- リソースをクリーンアップする

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な前提条件は特にありません。
* 無料価格プランを使用するか、Azure サブスクリプションを使用することができます。

## <a name="create-a-smart-meter-monitoring-application"></a>スマート メーター監視アプリケーションを作成する

1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[Energy]\(エネルギー\)** タブを選択します。

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-build.png" alt-text="スマート メーターのテンプレート":::

1. **[Smart meter monitoring]\(スマート メーター監視\)** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboard"></a>ダッシュボード

デプロイされたアプリケーション テンプレートには、サンプルのスマート メーター デバイス、デバイス モデル、およびダッシュボードが付属しています。 

Adatum は、スマート メーターを監視および管理する架空のエネルギー会社です。 スマート メーター監視ダッシュボードには、スマート メーターのプロパティ、データ、サンプル コマンドが表示されます。 これにより、オペレーターおよびサポート チームは、サポート インシデントに備えて、事前に次のアクティビティを実行できます。 
* マップ上の最新のメーター情報とその設置[場所](../core/howto-use-location-data.md)を確認する
* メーター ネットワークと接続の状態を事前に確認する 
* ネットワークの正常性の最小および最大の電圧の測定値を監視する 
* エネルギー、電力、および電圧の傾向を確認して、異常なパターンを把握する 
* 計画と課金のためのエネルギー総消費量を追跡する
* 再接続メーターやファームウェア バージョンの更新などのコマンドと制御操作。 テンプレートでは、コマンド ボタンには使用可能な機能が表示され、実際のコマンドは送信されません。 

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png" alt-text="スマート メーター監視ダッシュボード。":::

### <a name="devices"></a>デバイス

アプリには、サンプルのスマート メーター デバイスが付属しています。 デバイスの詳細を確認するには、 **[デバイス]** タブをクリックします。

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-devices.png" alt-text="スマート メーター デバイス。":::

サンプル デバイス **SM0123456789** リンクをクリックすると、デバイスの詳細が表示されます。 **[プロパティの更新]** ページ上でデバイスの書き込み可能なプロパティを更新し、ダッシュボードで更新された値を視覚化できます。

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png" alt-text="スマート メーターのプロパティ。":::

### <a name="device-template"></a>デバイス テンプレート

**[デバイス テンプレート]** タブをクリックして、スマート メーター デバイス モデルを表示します。 モデルには、データ、プロパティ、コマンド、およびビューの事前定義インターフェイスがあります。

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-template.png" alt-text="スマート メーター デバイス テンプレート。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションの引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. 左側のペインで、[管理] タブを開きます。
1. [アプリケーションの設定] を選択し、ページの下部にある [削除] ボタンをクリックします。 

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png" alt-text="アプリケーションを削除する。":::

## <a name="next-steps"></a>次のステップ

> [チュートリアル: ソーラー パネル アプリケーション テンプレートをデプロイして利用する](tutorial-solar-panel-app.md)

