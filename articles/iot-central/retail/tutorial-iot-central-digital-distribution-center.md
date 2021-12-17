---
title: チュートリアル - Azure IoT のデジタル配布センター | Microsoft Docs
description: このチュートリアルでは、IoT Central のデジタル配布センター アプリケーション テンプレートをデプロイして使用する方法について説明します
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 70ea24b64a1173d2a397d9603d020506f53be37e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434666"
---
# <a name="tutorial-deploy-and-walk-through-the-digital-distribution-center-application-template"></a>チュートリアル: デジタル配布センター アプリケーション テンプレートをデプロイして調べる

IoT Central の "*デジタル配布センター*" アプリケーション テンプレートとこの記事のガイダンスを使用して、エンドツーエンドのデジタル配布センター ソリューションを開発します。

   :::image type="content" source="media/tutorial-iot-central-ddc/digital-distribution-center-architecture.png" alt-text="デジタル配布センター。":::

1. ゲートウェイ デバイスにテレメトリ データを送信している IoT センサーのセット
2. テレメトリと集計された分析情報を IoT Central に送信しているゲートウェイ デバイス
3. データは、操作のために目的の Azure サービスにルーティングされます
4. ASA や Azure Functions などの Azure サービスを使用して、データ ストリームの形式を再設定し、目的のストレージ アカウントに送信できます
5. 処理されたデータは、ほぼリアルタイムのアクションのためにホット ストレージに格納されるか、ML やバッチ分析に基づいて分析情報をさらに拡充するためにコールド ストレージに格納されます。 
6. Logic Apps を使用すると、エンド ユーザーのビジネス アプリケーションでさまざまなビジネス ワークフローを利用できます

### <a name="video-cameras"></a>ビデオ カメラ 

ビデオ カメラは、このデジタル接続されたエンタープライズ規模のエコシステムにおける主要なセンサーです。 機械学習と人工知能の進歩によって、ビデオを構造化データに変換し、クラウドに送信する前にエッジで処理できるようになります。 IP カメラを使用して画像をキャプチャし、カメラで圧縮してから、エッジ コンピューティング経由で圧縮データをビデオ分析パイプラインに送信できます。または、GigE ビジョン カメラを使用してセンサー上の画像をキャプチャした後、これらの画像を直接 Azure IoT Edge に送信し、ビデオ分析パイプラインで処理する前に圧縮することができます。 

### <a name="azure-iot-edge-gateway"></a>Azure IoT Edge ゲートウェイ

"センサーとしてのカメラ" およびエッジ ワークロードは Azure IoT Edge によってローカルに管理され、カメラ ストリームは分析パイプラインによって処理されます。 Azure IoT Edge のビデオ分析処理パイプラインには、応答時間の短縮や帯域幅の低消費などのさまざまな利点があり、低遅延になってデータ処理が迅速になります。 最も重要なメタデータ、分析情報、アクションだけがクラウドに送信され、さらに処理または調査されます。 

### <a name="device-management-with-iot-central"></a>IoT Central によるデバイス管理
 
Azure IoT Central はソリューション開発プラットフォームであり、IoT デバイスや Azure IoT Edge ゲートウェイの接続、構成、管理が簡単になります。 このプラットフォームを使うと、IoT デバイスの管理、運用、関連開発の負担とコストが大幅に削減されます。 顧客とパートナーは、エンドツーエンドのエンタープライズ ソリューションを構築し、配布センターでデジタル フィードバック ループを実現できます。

### <a name="business-insights-and-actions-using-data-egress"></a>データ エグレスを使用したビジネスの分析情報とアクション 

IoT Central プラットフォームでは、継続的データ エクスポート (CDE) と API を通じて豊富な機能拡張オプションが提供されます。 一般に、テレメトリ データ処理または未加工のテレメトリに基づくビジネス分析情報は、優先される基幹業務アプリケーションにエクスポートされます。 Webhook、サービス バス、イベント ハブ、または BLOB ストレージを使用して、機械学習モデルを構築、トレーニング、デプロイし、分析情報をさらに補強することによって、これを実現できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * デジタル配布センター アプリケーションを作成する。
> * アプリケーションを調べる。

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な特定の前提条件はありません
* Azure サブスクリプションを用意することをお勧めしますが、なくても試してみることはできます

## <a name="create-digital-distribution-center-application-template"></a>デジタル配布センター アプリケーション テンプレートを作成する

次の手順に従ってアプリケーションを作成します。

1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[Retail]\(小売り\)** タブを選択します。

   :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-home-page.png" alt-text="アプリの作成方法を示すスクリーンショット。":::

1. **[デジタル配布センター]** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる 

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboard"></a>ダッシュボード

既定のダッシュボードは、配布センターのオペレーターを対象とするポータルです。 Northwind Traders 社は、コンベヤ システムを管理する架空の配布センター ソリューション プロバイダーです。 

このダッシュボードには、1 つのゲートウェイと、IoT デバイスとして機能している 1 つのカメラが表示されます。 ゲートウェイでは、有効、無効、不明、サイズなどのパッケージに関するテレメトリと共に、関連付けられたデバイス ツイン プロパティが提供されています。 すべてのダウンストリーム コマンドは、カメラなどの IoT デバイスで実行されます。 このダッシュボードは、配布センターの重要なデバイス操作アクティビティが表示されるように事前に構成されています。

ダッシュボードは、Azure IoT ゲートウェイと IoT デバイスのデバイス管理機能が表示されるように、論理的に編成されています。  

* ゲートウェイ コマンドと制御タスクを実行できます
* ソリューションの一部であるすべてのカメラを管理します。
* ソリューションの一部であるすべてのカメラを管理します。
* ソリューションの一部であるすべてのカメラを管理します。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-dashboard.png" alt-text="デジタル配布センターのダッシュボードを示すスクリーンショット。":::

### <a name="device-template"></a>デバイス テンプレート

[デバイス テンプレート] タブをクリックすると、ゲートウェイ機能モデルが表示されます。 機能モデルは、 **[Camera]\(カメラ\)** と **[Digital Distribution Gateway]\(デジタル配布ゲートウェイ\)** という 2 つの異なるインターフェイスを中心に構成されています

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="アプリケーションのデジタル配布ゲートウェイ デバイス テンプレートを表示する画面のスクリーンショット。":::

**[Camera]\(カメラ\)** - このインターフェイスには、すべてのカメラ固有のコマンド機能が整理されています 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="デジタル配布ゲートウェイ デバイス テンプレートのカメラ インターフェイスを表示する画面のスクリーンショット。":::

**[Digital Distribution Gateway]\(デジタル配布ゲートウェイ\)** - このインターフェイスは、カメラから送られてくるすべてのテレメトリ、クラウドで定義されているデバイス ツインのプロパティ、ゲートウェイ情報を表します。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="デジタル配布ゲートウェイ デバイス テンプレートのデジタル配布ゲートウェイ インターフェイスを表示する画面のスクリーンショット。":::

### <a name="gateway-commands"></a>ゲートウェイ コマンド

このインターフェイスには、すべてのゲートウェイ コマンド機能が整理されています

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="デジタル配布ゲートウェイ デバイス テンプレートのゲートウェイ コマンド インターフェイスを表示する画面のスクリーンショット。":::

### <a name="rules"></a>ルール

[Rules]\(規則\) タブを選択して、このアプリケーション テンプレートに存在する 2 つの異なる規則を表示します。 これらの規則は、詳細な調査のためオペレーターにメールで通知するように構成されています。

 **[Too many invalid packages alert]\(多すぎる無効パッケージ アラート\)** - この規則は、コンベヤ システムを通過する無効なパッケージの数が多いことがカメラによって検出されるとトリガーされます。

**[Large package]\(大きいパッケージ\)** - この規則は、品質を検査できない大きなパッケージをカメラが検出するとトリガーされます。 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-rules.png" alt-text="デジタル配布センター アプリケーションの規則一覧を表示する画面のスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションをもう使わない場合は、 **[管理]**  >  **[アプリケーションの設定]** に移動し、 **[削除]** をクリックすることによって、アプリケーション テンプレートを削除します。

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-cleanup.png" alt-text="使い終えたアプリケーションを削除する方法を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

デジタル流通センター ソリューション アーキテクチャについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デジタル流通センターの概念](./architecture-digital-distribution-center.md)
