---
title: OMS による Azure Data Factory の監視 | Microsoft Docs
description: 分析のためにデータを Operations Management Suite (OMS) にルーティングすることによって、Azure Data Factory を監視する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42142446"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Operations Management Suite (OMS) による Azure Data Factory の監視

Azure Monitor と Azure Data Factory の統合を使用して、データを Operations Management Suite (OMS) にルーティングすることができます。 この統合は次のシナリオで役立ちます。

1.  Data Factory によって OMS に発行された充実したメトリックのセットに複雑なクエリを記述する必要があります。 OMS によってこれらのクエリにカスタム アラートを作成することもできます。

2.  データ ファクトリ全体を監視する必要があります。 複数のデータ ファクトリから 1 つの OMS ワークスペースにデータをルーティングすることができます。

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>作業の開始

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>診断設定と OMS ワークスペースの構成

自分のデータ ファクトリに対して診断設定を有効にします。

1.  **[Azure Monitor]** -> **[診断設定]** を選択し、データ ファクトリを選択して診断をオンにします。

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  OMS ワークスペースの構成を含めて、診断設定を指定します。

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Azure Marketplace から Azure Data Factory Analytics の OMS パックをインストールする

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

**[作成]** をクリックして、OMS ワークスペースと OMS ワークスペースの設定を選択します。

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>OMS を使用して Azure Data Factory メトリックを監視する

**Azure Data Factory Analytics** の OMS パックをインストールすると、次のメトリックを有効にする既定のセットのビューが作成されます。

- ADF の実行 - 1) Data Factory によるパイプラインの実行

- ADF の実行 - 2) Data Factory によるアクティビティの実行

- ADF の実行 - 3) Data Factory によるトリガーの実行

- ADF のエラー - 1) 上位 10 の Data Factory によるパイプラインのエラー

- ADF のエラー - 2) 上位 10 の Data Factory によるアクティビティのエラー

- ADF のエラー - 3) 上位 10 の Data Factory によるトリガーのエラー

- ADF の統計 - 1) 種類別のアクティビティの実行

- ADF の統計 - 2) 種類別のトリガーの実行

- ADF の統計 - 3) パイプラインの実行の最長期間

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

上述のメトリックの視覚化、これらのメトリックの背後にあるクエリの確認、クエリの編集、アラートの作成などを行うことができます。

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>次の手順

スクリプトの実行によるパイプラインの監視と管理の詳細については、[プログラムを使用したパイプラインの監視および管理](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically)に関するページを参照してください。
