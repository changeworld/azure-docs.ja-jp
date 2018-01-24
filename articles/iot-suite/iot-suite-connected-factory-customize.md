---
title: "コネクテッド ファクトリ ソリューションをカスタマイズする - Azure | Microsoft Docs"
description: "コネクテッド ファクトリ事前構成済みソリューションの動作をカスタマイズする方法の説明です。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 48c8036d0bc9534ce94529b96d32b004769246c1
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>コネクテッド ファクトリ ソリューションによる OPC UA サーバー データの表示方法をカスタマイズする

コネクテッド ファクトリ ソリューションは、自身に接続されている OPC UA サーバーのデータを集計して表示します。 このソリューションでは OPC UA サーバーを参照し、コマンドを送信することができます。 OPC UA の詳細については、[コネクテッド ファクトリの FAQ](iot-suite-faq-cf.md) に関するページをご覧ください。

ソリューションの集計データの例には、ダッシュボードで確認できるファクトリ レベル、ライン レベル、およびステーション レベルの総合設備効率 (OEE) と主要業績評価指標 (KPI) が含まれます。 次のスクリーンショットは、**ミュンヘン** ファクトリの**生産ライン 1** にある**アセンブリ** ステーションの OEE 値と KPI 値を示しています。

![ソリューションの OEE 値と KPI 値の例][img-oee-kpi]

このソリューションを使用すると、"*ステーション*" と呼ばれる OPC UA サーバーの特定のデータ項目に関する詳細情報を表示できます。 次のスクリーンショットは、特定のステーションの製造アイテム数のプロットを示しています。

![製造アイテム数のプロット][img-manufactured-items]

グラフのいずれかをクリックすると、Time Series Insights (TSI) を使用して、データをさらに詳しく確認できます。

![Time Series Insights を使用したデータ詳細の確認][img-tsi]

この記事では、次の内容について説明します。

- ソリューションのさまざまなビューでデータを使用できるようにする方法。
- ソリューションによるデータの表示方法をカスタマイズする方法。

## <a name="data-sources"></a>データ ソース

コネクテッド ファクトリ ソリューションは、自身に接続されている OPC UA サーバーのデータを表示します。 既定のインストールには、ファクトリ シミュレーションを実行する複数の OPC UA サーバーが含まれています。 ソリューションに[ゲートウェイ経由で接続][lnk-connect-cf]する独自の OPC UA サーバーを追加できます。

ダッシュボードでは、接続された OPC UA サーバーからソリューションに送信されるデータ項目を参照できます。

1. **[ブラウザー]** を選択して、**[Select an OPC UA server (OPC UA サーバーの選択)]** ビューに移動します。

    ![[Select an OPC UA server (OPC UA サーバーの選択)] ビュー][img-select-server]

1. サーバーを選択し、**[接続]** をクリックします。 セキュリティの警告が表示されたら、**[続行]** をクリックします。

    > [!NOTE]
    > この警告はサーバーごとに 1 回だけ表示され、ソリューション ダッシュボードとサーバーの間の信頼関係を確立します。

1. これで、サーバーがソリューションに送信できるデータ項目を参照できます。 ソリューションに送信中の項目にはチェック マークが付いています。

    ![公開されている項目][img-published]

1. ソリューションの "*管理者*" は、データ項目を公開して、コネクテッド ファクトリ ソリューションで使用できるようにすることを選択できます。 また、データ項目の値を変更したり、OPC UA サーバーでメソッドを呼び出したりすることもできます。

## <a name="map-the-data"></a>データのマップ

コネクテッド ファクトリ ソリューションでは、OPC UA サーバーの公開データ項目をソリューションのさまざまなビューにマップしたり、ビューで集計したりできます。 コネクテッド ファクトリ ソリューションは、ソリューションのプロビジョニング時に Azure アカウントにデプロイされます。 Visual Studio コネクテッド ファクトリ ソリューションの JSON ファイルには、このマッピング情報が格納されます。 この JSON 構成ファイルは、Visual Studio コネクテッド ファクトリ ソリューションで表示および変更できます。 ソリューションは、変更後に再デプロイできます。

構成ファイルを使用すると、次の操作を行うことができます。

- シミュレートされた既存のファクトリ、生産ライン、ステーションを編集する。
- ソリューションに接続する実際の OPC UA サーバーからデータをマップする。

特定の要件を満たすためにデータをマッピングしたり集計したりする方法については、「[How to configure the Connected factory preconfigured solution (コネクテッド ファクトリ事前構成済みソリューションの構成方法)](iot-suite-connected-factory-configure.md)」をご覧ください。

## <a name="deploy-the-changes"></a>変更のデプロイ

**ContosoTopologyDescription.json** ファイルに対する変更が完了したら、コネクテッド ファクトリ ソリューションを Azure アカウントに再デプロイする必要があります。

**azure-iot-connected-factory** リポジトリには、ソリューションの再構築とデプロイに使用できる **build.ps1** PowerShell スクリプトが含まれています。

## <a name="next-steps"></a>次の手順

コネクテッド ファクトリ事前構成済みソリューションの詳細については、次の記事を参照してください。

* [コネクテッド ファクトリ事前構成済みソリューションのチュートリアル][lnk-rm-walkthrough]
* [コネクティッド ファクトリ用のゲートウェイをデプロイする][lnk-connect-cf]
* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]
* [コネクテッド ファクトリに関する FAQ](iot-suite-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md