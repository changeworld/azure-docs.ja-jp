---
title: Azure Time Series Insights で OPC UA データを視覚化する
description: このチュートリアルでは、Time Series Insights を使用してデータを視覚化する方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787352"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>チュートリアル: Time Series Insights (TSI) を使用してデータを視覚化する

OPC Publisher モジュールは、OPC UA サーバーに接続し、これらのサーバーから IoT Hub にデータを発行します。 Industrial IoT プラットフォームのテレメトリ プロセッサにより、これらのイベントが処理され、コンテキスト化されたサンプルが TSI と他のコンシューマーに転送されます。  

このハウツー ガイドでは、この Time Series Insights 環境を使用して OPC UA テレメトリを視覚化および分析する方法を示します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * すべてのチュートリアルには、完了までの手順を要約した一覧が含まれます
> * これらの各箇条書きは、キー H2 に位置を合わせます
> * チュートリアルでは、これらの緑色のチェックボックスを使用します

## <a name="prerequisite"></a>前提条件

* IIoT Platform をデプロイして、自動的に作成された Time Series Insights 環境を用意する
* データが IoT Hub に発行されている

## <a name="time-series-insights-explorer"></a>Time Series Insights エクスプローラー

Time Series Insights エクスプローラーは、テレメトリを視覚化するために使用できる Web アプリです。 アプリケーションの URI を取得するには、デプロイの結果として保存された `.env` ファイルを開きます。  ブラウザーを開き、`PCS_TSI_URL` 変数内の URL にアクセスします。  

Time Series Insights エクスプローラーを使用する前に、データを視覚化する権利があるユーザーに TSI データへのアクセス権を付与する必要があります。 新しいデプロイではデータ アクセス ポリシーが既定で設定されていないため、誰もデータを表示できないことに注意してください。 データ アクセス ポリシーは、Azure portal で、IIoT のプラットフォームにデプロイ済みのリソース グループにデプロイされた Time Series Insights 環境に次のように設定する必要があります。

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

[データ アクセス ポリシー] を選択します。

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

必要なユーザーを割り当てます。

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


TSI Explorer で、未割り当ての時系列インスタンスに注目してください。 TSI インスタンスは、OPC サーバー内の発行されたノードから生成された特定のデータ ポイントの時間または値の系列に対応します。 TSI インスタンスは、それぞれ OPC UA データ ポイントであり、EndpointId、SubscriptionId、および NodeId によって一意に識別されます。 TSI インスタンスのモデルは、IIoT プラットフォーム テレメトリ プロセッサのイベント ハブから取り込まれた利用統計情報に基づいて、自動的に検出され、エクスプローラーに表示されます。

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

利用統計情報をグラフで視覚化するには、TSI インスタンスを右クリックし、値を選択します。 グラフで使用される時間枠は、右上隅から調整できます。 複数のインスタンスの値は、同じ時間単位の選択で視覚化できます。

詳細については、[クイック スタート: Azure Time Series Insights プレビューの探索](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)に関する記事を参照してください

## <a name="define-and-apply-a-new-model"></a>新しいモデルを定義して適用する

テレメトリ インスタンスは現時点では、単に未加工の形式であるため、適切にコンテキスト化する必要があります 

TSI モデルの詳細については、[Azure Time Series Insights プレビューの時系列モデル](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)に関する記事を参照してください

1. 手順 1 - Explorer のモデル タブで、取り込まれた利用統計情報の新しい階層を定義します。 階層とは、TSI インスタンスを介したより直観的なナビゲーションに必要なメタ情報をユーザーが挿入できるようにする論理ツリー構造です。 ユーザーは、後でさまざまな TSI インスタンスに対してインスタンス化できる階層テンプレートを作成、削除、変更できます。

   ![ステップ 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. 手順 2 - 値の新しい型を定義します。 この例では、数値データ型のみを処理します

   ![ステップ 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. 手順 3 - 以前に定義した階層に分類する必要がある新しい TSI インスタンスを選択します

   ![ステップ 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. 手順 4 - 論理構造と一致させるために、インスタンスのプロパティ (名前、説明、データ値) と階層のフィールドに値を入力します 

   ![手順 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. 手順 5 - すべての未分類の TSI インスタンスに対して手順 5 を繰り返します

   ![手順 5.](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. 手順 6 - TSI Explorer のメイン ページに戻り、分類されたインスタンス階層を順番に参照して、分析するデータ ポイントの値を選択します

   ![手順 6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Power BI への Time Series Insights の接続

Time Series Insights 環境を Power BI に接続することもできます。  詳細については、[TSI を Power BI に接続する方法](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)と [Power BI で TSI からのデータを視覚化する方法](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi)に関する各記事を参照してください。


## <a name="next-steps"></a>次のステップ
TSI でデータを視覚化する方法を学んだので、Industrial IoT GitHub リポジトリを確認できます。

> [!div class="nextstepaction"]
> [IIoT Platform GitHub リポジトリ](https://github.com/Azure/iot-edge-opc-publisher)