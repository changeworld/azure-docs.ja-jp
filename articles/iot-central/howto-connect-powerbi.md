---
title: Azure IoT Central データを Power BI ダッシュボードに視覚化する |Microsoft Docs
description: Azure IoT Central Analytics Power BI ソリューション テンプレートを使用して、IoT Central データを視覚化して分析します。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6f8bed8aba43d77cdfad49f4fe62a4c7aa2c5ce3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368071"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central データを Power BI ダッシュボードに視覚化する

*このトピックは管理者向けです。*

![Power BI ソリューション テンプレートのパイプライン](media/howto-connect-powerbi/iot-continuous-data-export.png)

Azure IoT Central Analytics Power BI ソリューション テンプレートを使用して、IoT デバイスのパフォーマンスを監視するための強力な Power BI ダッシュボードを作成します。 Power BI ダッシュ ボードでは、以下を実行できます。
- デバイスが特定の時間に送信しているデータの量を追跡する
- テレメトリ、状態、およびイベント間でデータ ボリュームを比較する
- 多数の測定値を報告しているデバイスを識別する
- デバイスの測定値の過去の傾向を観察する
- 多数の重要なイベントを送信している、問題のあるデバイスを識別する

このソリューション テンプレートは、[データの連続エクスポート](howto-export-data.md)から Azure Blob Storage アカウントにデータを取り込むパイプラインを設定します。 このデータが Azure Functions、Azure Data Factory、Azure SQL Database を通って、データの処理と変換が行われます。 その出力は、PBIX ファイルとしてダウンロード可能な Power BI レポートとして視覚化し、分析することができます。 これらのリソースは、すべてが Azure サブスクリプションで作成されるため、各コンポーネントをニーズに合わせてカスタマイズできます。 このソリューション テンプレートは完全なオープン ソースであるため、アーキテクチャの詳細とソリューションの拡張方法を [GitHub リポジトリ](https://aka.ms/iotcentralgithubpowerbisolutiontemplate)で確認できます。

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Microsoft AppSource から [Azure IoT Central Analytics ソリューション テンプレート](https://aka.ms/iotcentralpowerbisolutiontemplate)を取得します。

## <a name="prerequisites"></a>前提条件
このテンプレートを設定するには、以下が必要です。
- Azure サブスクリプションへのアクセス。
- [データの連続エクスポート](howto-export-data.md)を使用して IoT Central アプリからエクスポートされるデータ。 Power BI ダッシュボードを最大限に活用するには、測定値、デバイス、およびデバイス テンプレート ストリームを有効にすることをお勧めします。
- Power BI Desktop (最新バージョン)
- Power BI Pro (ダッシュボードを他のユーザーと共有する場合)

## <a name="reports"></a>レポート

2 つのレポートが自動的に生成されます。 

最初のレポートは、デバイスによって報告された測定値の履歴を示し、測定値の種類別と、最も高い測定値を送信しているデバイス別に分類します。

![Power BI レポート (1 ページ目)](media/howto-connect-powerbi/template-page1-hasdata.PNG)

2 つ目のレポートは、イベントに深く踏み込み、報告されたエラーと警告の履歴ビューを示します。 報告されているイベントの合計数が最も多いデバイスと、具体的なエラー イベントと警告イベントの数も示されます。

![Power BI レポート (2 ページ目)](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>リソース

AppSource で、[Azure IoT Central Analytics ソリューション テンプレート](https://aka.ms/iotcentralpowerbisolutiontemplate)を取得します。

[GitHub リポジトリ](https://aka.ms/iotcentralgithubpowerbisolutiontemplate)で、アーキテクチャとソリューションの拡張法を確認します。

## <a name="next-steps"></a>次の手順

ここでは、Power BI にデータを視覚化する方法を説明しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [デバイスを管理する方法](howto-manage-devices.md)
