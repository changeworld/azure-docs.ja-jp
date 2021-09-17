---
title: チュートリアル - Azure IoT ソーラー パネルの監視 | Microsoft Docs
description: このチュートリアルでは、IoT Central 用のソーラー パネルの監視アプリケーション テンプレートをデプロイして使用する方法について説明します。
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59650af808a5af947cbb2fd6df1f203692fb7034
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182780"
---
# <a name="tutorial-deploy-and-walk-through-the-solar-panel-monitoring-app-template"></a>チュートリアル: ソーラーパネルの監視アプリ テンプレートをデプロイして利用する 

IoT Central の "*ソーラー パネルの監視*" アプリケーション テンプレートとこの記事のガイダンスを使用して、エンドツーエンドのソーラー パネルの監視ソリューションを開発します。

  :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-app-architecture.png" alt-text="ソーラー パネルのアーキテクチャ。":::

このアーキテクチャは、次のコンポーネントで構成されます。 アプリケーションの中には、以下に一覧表示するすべてのコンポーネントを必要としないものもあります。

### <a name="solar-panels-and-connectivity"></a>ソーラー パネルと接続

ソーラー パネルは、再生可能エネルギーの重要なソースの 1 つです。 ソーラー パネルの種類と設定に応じて、ゲートウェイまたは他の中間デバイスや独自のシステムを使用して接続できます。 直接接続できないデバイスを接続するように IoT Central デバイス ブリッジを構築する必要がある場合があります。 IoT Central デバイス ブリッジはオープン ソース ソリューションであり、詳細については[こちら](../core/howto-build-iotc-device-bridge.md)で確認できます。 

### <a name="iot-central-platform"></a>IoT Central プラットフォーム

Azure IoT Central は、IoT ソリューションの構築を簡素化し、IoT の管理、運用、開発の負担とコストを削減するために役立つプラットフォームです。 IoT Central を使用すると、大規模なモノのインターネット (IoT) アセットを簡単に接続、監視、管理できます。 ソーラー パネルを IoT Central に接続すると、アプリ テンプレートにはデバイス モデル、コマンド、ダッシュボードなどの組み込みの機能が使用されます。 また、アプリ テンプレートには、ほぼリアルタイムのメーター データの監視、分析、ルール、視覚化など、ウォーム パス シナリオにも IoT Central ストレージが使用されます。

### <a name="extensibility-options-to-build-with-iot-central"></a>IoT Central を使用して構築する拡張オプション

IoT Central プラットフォームには、次の 2 つの拡張オプションがあります。継続的データ エクスポート (CDE) と API。 顧客とパートナーは、特定のニーズに合わせてこれらのオプションのいずれかを選択し、ソリューションをカスタマイズすることができます。 たとえば、あるパートナーは Azure Data Lake Storage (ADLS) を使用して CDE を構成しました。 バッチ処理、監査、報告などの目的で、長期的なデータ保持やその他のコールド パス ストレージ シナリオに ADLS が使用されています。 


このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]

> * ソーラー パネル アプリを無料で作成する
> * アプリケーションを調べる
> * リソースをクリーンアップする


## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な前提条件は特にありません。
* 無料価格プランを使用するか、Azure サブスクリプションを使用することができます。


## <a name="create-a-solar-panel-monitoring-application"></a>ソーラー パネルの監視アプリケーションを作成する


1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[Energy]\(エネルギー\)** タブを選択します。

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-build.png" alt-text="スマート メーターのテンプレート":::

1. **[Solar panel monitoring]\(ソーラー パネルの監視\)** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboard"></a>ダッシュボード

アプリケーション テンプレートをデプロイしたら、アプリについてもう少し詳しく調べてみましょう。 サンプルのスマート メーター デバイス、デバイス モデル、ダッシュボードが付属していることに注目してください。

Adatum は、ソーラー パネルを監視および管理する架空のエネルギー会社です。 ソーラー パネル監視ダッシュボードには、ソーラー パネルのプロパティ、データ、およびサンプル コマンドが表示されます。 このダッシュボードでは、お客様またはそのサポート チームが次のアクティビティを行って、問題に追加のサポートが必要となる前にプロアクティブに対応できます。
* マップ上の最新のパネル情報とその設置[場所](../core/howto-use-location-data.md)を確認する。
* パネルの状態と接続の状態を確認する。
* エネルギー生成と温度の傾向を確認して異常なパターンをキャッチする。
* 計画と課金のためにエネルギー生成の合計を追跡する。
* パネルをアクティブにして、ファームウェア バージョンを更新する (必要な場合)。 テンプレートのコマンド ボタンは使用可能な機能を表示するだけで、実際のコマンドは送信されません。

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png" alt-text="ソーラー パネルの監視テンプレートのダッシュボードのスクリーンショット。":::

### <a name="devices"></a>デバイス

このアプリには、サンプルのソーラー パネル デバイスが付属しています。 デバイスの詳細を表示するには、 **[デバイス]** を選択します。

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device.png" alt-text="ソーラー パネルの監視テンプレートの [デバイス] のスクリーンショット。":::

サンプル デバイス (**SP0123456789**) を選択します。 **[プロパティの更新]** タブからデバイスの書き込み可能なプロパティを更新し、更新した値をダッシュボードで視覚的に確認できます。 

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png" alt-text="ソーラー パネルの監視テンプレートの [プロパティの更新] タブのスクリーンショット。":::


### <a name="device-template"></a>デバイス テンプレート

ソーラー パネルのデバイス モデルを確認するには、 **[デバイス テンプレート]** タブを選択します。モデルには、データ、プロパティ、コマンド、およびビューの事前定義インターフェイスがあります。

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png" alt-text="ソーラー パネルの監視テンプレートのデバイス テンプレートのスクリーンショット。":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後このアプリケーションを使用しない場合は、次の手順でアプリケーションを削除します。

1. 左ペインから **[管理]** を選択します。
1. **[アプリケーションの設定]**  >  **[削除]** の順に選択します。 

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png" alt-text="ソーラー パネルの監視テンプレートの [管理] のスクリーンショット。":::
