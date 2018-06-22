---
title: 予測メンテナンスのソリューション アクセラレータのチュートリアル - Azure | Microsoft Docs
description: Azure IoT 予測メンテナンスのソリューション アクセラレータのチュートリアル。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: e29975558801b4ffccd38d4485306d25ecaec0aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659959"
---
# <a name="predictive-maintenance-solution-accelerator-walkthrough"></a>予測メンテナンスのソリューション アクセラレータのチュートリアル

予測メンテナンスのソリューション アクセラレータは、障害が発生する可能性があるポイントを予測するビジネス シナリオに対応したエンド ツー エンド ソリューションです。 このソリューション アクセラレータを使用すると、メンテナンスの最適化などのアクティビティを先手を打って実行できます。 このソリューションは、IoT Hub、Stream Analytics、[Azure Machine Learning][lnk-machine-learning] ワークスペースなど、主要な Azure IoT ソリューション アクセラレータ サービスを組み合わせたものです。 このワークスペースには、公開されているサンプル データ セットに基づいて航空機エンジンの残存耐用年数 (RUL) を予測するモデルが含まれています。 このソリューションでは、固有のビジネス要件を満たすソリューションを計画および実装するための開始地点として使用できる、IoT ビジネス シナリオが完全に実装されています。

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、ソリューション アクセラレータの論理コンポーネントの概要を示したものです。

![][img-architecture]

青色の項目は、ソリューション アクセラレータをデプロイしたリージョンにプロビジョニングされた Azure サービスです。 ソリューション アクセラレータをデプロイできるリージョンの一覧は、[プロビジョニング ページ][lnk-azureiotsuite]に表示されます。

緑色の項目は、シミュレートされている航空機エンジン デバイスです。 これらのシミュレートされているデバイスの詳細については、「[シミュレートされたデバイス](#simulated-devices)」のセクションを参照してください。

灰色の項目は、"*デバイスの管理*" 機能を実装しているコンポーネントを示します。 予測メンテナンスのソリューション アクセラレータの現在のリリースでは、これらのリソースはプロビジョニングできません。 デバイスの管理の詳細については、[リモート監視ソリューション アクセラレータ][lnk-remote-monitoring]に関するページを参照してください。

## <a name="simulated-devices"></a>シミュレートされたデバイス

ソリューション アクセラレータでは、航空機エンジン デバイスをシミュレートしています。 このソリューションでは、1 台の航空機にマッピングされる 2 つのエンジンをプロビジョニングしています。 各エンジンは、4 種類のテレメトリを出力します。センサー 9、センサー 11、センサー 14、センサー 15 は、Machine Learning モデルがそのエンジンの RUL を計算するために必要なデータを提供します。 シミュレートされたデバイスはそれぞれ、次のテレメトリ メッセージを IoT Hub に送信します。

"*サイクル数*"。 サイクルは、2 ～ 10 時間の期間で完了したフライトを表します。 フライト中、テレメトリ データは 30 分ごとにキャプチャされます。

*テレメトリ*。 エンジン属性を示すセンサーは 4 つあります。 センサーは総称的に、センサー 9、センサー 11、センサー 14、およびセンサー 15 とラベル付けされています。 これらの 4 つのセンサーは、RUL モデルから有用な結果を得るために十分なテレメトリを表します。 ソリューション アクセラレータで使用されるモデルは、実際のエンジンのセンサー データなど、公開されているデータ セットから作成されています。 元のデータ セットからこのモデルを作成する方法の詳細については、「[Cortana Intelligence Gallery Predictive Maintenance Template (Cortana Intelligence ギャラリーの予測的なメンテナンス テンプレート)][lnk-cortana-analytics]」を参照してください。

シミュレーション デバイスは、ソリューションの IoT Hub から送信された次のコマンドを処理することができます。

| コマンド | 説明 |
| --- | --- |
| StartTelemetry |シミュレーションの状態を制御します。<br/>デバイスのテレメトリ送信を開始します。 |
| StopTelemetry |シミュレーションの状態を制御します。<br/>デバイスのテレメトリ送信を停止します。 |

IoT Hub は、デバイスのコマンドの受信確認を渡します。

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics ジョブ

**ジョブ: テレメトリ**は、次の 2 つのステートメントを使用して、デバイスのテレメトリの受信ストリームに対して動作します。

* 1 つ目では、デバイスからのすべてのテレメトリを選択し、このデータを Blob Storage に送信します。 ここから、Web アプリで視覚化されます。
* 2 つ目では、2 分間のスライディング ウィンドウに渡る平均センサー値を計算し、このデータを Event Hub を介して**イベント プロセッサ**に送信します。

## <a name="event-processor"></a>イベント プロセッサ
**イベント プロセッサ ホスト**は、Azure Web Job で実行されます。 **イベント プロセッサ** は、完了したサイクルの平均センサー値を受け取ります。 それらの値は、トレーニングされたモデルを公開する API に渡され、エンジンの RUL が計算されます。 この API は、ソリューションの一部としてプロビジョニングされた Machine Learning ワークスペースによって公開されます。

## <a name="machine-learning"></a>Machine Learning
Machine Learning コンポーネントは、実際の航空機エンジンから収集されたデータから派生したモデルを使用します。 この Machine Learning ワークスペースには、[azureiotsuite.com][lnk-azureiotsuite] ページのソリューションのタイルから移動することも可能です。 このタイルは、ソリューションが**準備完了**の状態の場合に使用できます。


## <a name="next-steps"></a>次の手順
予測メンテナンスのソリューション アクセラレータの主要コンポーネントは確認しました。次は、それをカスタマイズします。

IoT ソリューション アクセラレータの他の機能についても学習できます。

* [IoT ソリューション アクセラレータに関してよく寄せられる質問][lnk-faq]
* [IoT の徹底的なセキュリティ][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-accelerators-remote-monitoring-explore.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/