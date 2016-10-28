<properties
 pageTitle="予測的なメンテナンスのチュートリアル | Microsoft Azure"
 description="Azure IoT の予測的なメンテナンスの構成済みソリューションのチュートリアルです。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# 予測的なメンテナンスの構成済みソリューションのチュートリアル

## はじめに

IoT Suite の予測的なメンテナンスの構成済みソリューションは、障害が発生する可能性があるポイントを予測するビジネス シナリオに対応したエンド ツー エンド ソリューションです。この構成済みソリューションを使用すると、メンテナンスの最適化などのアクティビティを先手を打って実行できます。このソリューションは、[Azure Machine Learning][lnk_machine_learning] ワークスペースなどの主要な Azure IoT Suite サービスを組み合わせたものです。このワークスペースには、公開されているサンプル データ セットに基づいて航空機エンジンの残存耐用年数 (RUL) を予測する実験が含まれています。このソリューションでは、固有のビジネス要件を満たすソリューションを計画および実装するための開始地点として使用できる、IoT ビジネス シナリオが完全に実装されています。

## 論理アーキテクチャ

次の図は、事前構成済みソリューションの論理コンポーネントの概要を示したものです。

![][img-architecture]

青色の項目は、構成済みのソリューションをプロビジョニングするために選択した場所に、プロビジョニングされている Azure サービスです。構成済みのソリューションは、米国東部、北ヨーロッパまたは東アジア リージョンのいずれかにプロビジョニングできます。

構成済みのソリューションをプロビジョニングするリージョンによっては、一部のリソースは利用できない場合があります。図のオレンジ色の項目は、選択したリージョンから最も近い利用可能なリージョン (米国中南部、西ヨーロッパや東南アジア) にプロビジョニングされている Azure サービスを示します。

緑色の項目は、シミュレートされている航空機エンジン デバイスです。これらのシミュレートされているデバイスの詳細については、次のセクションを参照してください。

灰色の項目は、*デバイスの管理*機能を実装しているコンポーネントを示します。予測的なメンテナンスの構成済みソリューションの現在のリリースでは、これらのリソースはプロビジョニングできません。デバイスの管理の詳細については、「[リモート監視事前構成済みソリューション][lnk-remote-monitoring]」を参照してください。

## シミュレートされたデバイス

構成済みのソリューションでは、航空機エンジン デバイスをシミュレーションしています。このソリューションでは、1 台の航空機にマッピングされる 2 つのエンジンをプロビジョニングしています。各エンジンは、4 種類のテレメトリを出力します。センサー 9、センサー 11、センサー 14、センサー 15 は、Machine Learning モデルがそのエンジンの残存耐用年数 (RUL) を計算するために必要なデータを提供します。シミュレートされたデバイスはそれぞれ、次のテレメトリ メッセージを IoT Hub に送信します。

*サイクル数*。サイクルとは、2 ～ 10 時間以内に完了しているフライトを表します。フライト中、テレメトリ データは 30 分ごとにキャプチャされます。

*テレメトリ*。エンジン属性を示すセンサーは 4 つあります。センサーは総称的に、センサー 9、センサー 11、センサー 14、およびセンサー 15 とラベル付けされています。これら 4 つのセンサーは、RUL 用に Machine Learning モデルから有用な結果を得るテレメトリを表します。このモデルは、実際のエンジンのセンサー データなど、公開されているデータ セットから作成されています。元のデータ セットからこのモデルを作成する方法の詳細については、「[Cortana Intelligence Gallery Predictive Maintenance Template (Cortana Intelligence ギャラリーの予測的なメンテナンス テンプレート)][lnk-cortana-analytics]」を参照してください。

シミュレートされたデバイスは、IoT Hub から送信された次のコマンドを処理できます。

| コマンド | Description |
|---------|-------------|
| StartTelemetry | シミュレーションの状態を制御します。<br/>テレメトリを送信するデバイスを起動します。 |
| StopTelemetry | シミュレーションの状態を制御します。<br/>テレメトリを送信するデバイスを終了します。 |

IoT Hub は、デバイスのコマンドの受信確認を渡します。

## Azure Stream Analytics ジョブ

**ジョブ: テレメトリ**は、2 つのステートメントを使用して、デバイスのテレメトリの受信ストリームに対して動作します。1 つ目では、デバイスからのすべてのテレメトリを選択し、Web アプリで視覚化されるデータが格納される Blob Storage に送信します。2 つ目のステートメントは 2 分間のスライディング ウィンドウに渡る平均センサー値を計算し、このデータを Event Hub を介して**イベント プロセッサ**に送信します。

## イベント プロセッサ

**イベント プロセッサ**は、完了したサイクルの平均センサー値を受け取ります。それらの値は、Machine Learning でトレーニングされたモデルを公開する API に渡され、エンジンの RUL が計算されます。

## Azure Machine Learning

元のデータ セットからこのモデルを作成する方法の詳細については、「[Cortana Intelligence Gallery Predictive Maintenance Template (Cortana Intelligence ギャラリーの予測的なメンテナンス テンプレート)][lnk-cortana-analytics]」を参照してください。

## チュートリアルの開始

このセクションでは、ソリューションのコンポーネントと想定されるユース ケースについて説明し、例を示します。

### 予測的なメンテナンスのダッシュボード

Web アプリケーションのこのページでは、PowerBI JavaScript コントロールを使用して、以下を視覚化しています (「[PowerBI の視覚表現リポジトリ][lnk-powerbi]」を参照)。

- Blob Storage 内の Stream Analytics ジョブから出力されたデータ。
- 各航空機エンジンの RUL とサイクル数。

### クラウド ソリューションの動作を確認する

Azure ポータルで、指定したソリューション名の付いたリソース グループに移動して、プロビジョニングされたリソースを確認します。

![][img-resource-group]

構成済みのソリューションをプロビジョニングすると、Machine Learning ワークスペースへのリンクを含む電子メールを受信します。この Machine Learning ワークスペースには、**準備完了**の状態の場合、プロビジョニングしたソリューションの [azureiotsuite.com][lnk-azureiotsuite] ページから移動することも可能です。

![][img-machine-learning]

ソリューション ポータルでは、シミュレートされた 4 つのデバイスがサンプルにプロビジョニングされていることがわかります。2 機の航空機それぞれにエンジンが 2 つあり、エンジンごとにセンサーが 4 つあります。ソリューション ポータルに最初に移動すると、シミュレーションは停止します。

![][img-simulation-stopped]

**[シミュレーションの開始]** をクリックすると、シミュレーションが開始され、センサーの履歴、RUL、サイクル数、RUL 履歴がダッシュボードに入力されます。

![][img-simulation-running]

RUL が (デモンストレーション用に選択した任意のしきい値である) 160 未満の場合、ソリューション ポータルには RUL の表示の横に警告記号が示され、航空機エンジンは黄色で強調表示されます。RUL 値は、全体的には一般的な下降傾向を示していますが、上下する傾向があることがわかります。この動きはさまざまな長さのサイクルとモデル精度が含まれていることの結果です。

![][img-simulation-warning]

148 サイクルの完全なシミュレーションの完了には約 35 分かかります。160 の RUL のしきい値には約 5 分で最初に達し、両エンジンがしきい値に達するのは、約 8 分後です。

148 サイクルの完全なデータセットのシミュレーションが実行され、最終的な RUL とサイクル数が出されます。

シミュレーションは任意のポイントで停止できます。**[シミュレーションを開始する]** をクリックすると、データセットの先頭からのシミュレーションを再生できます。

## 次のステップ

これで予測的なメンテナンスの構成済みソリューションを実行しましたが、これを修正する場合は、「[事前構成済みソリューションのカスタマイズに関するガイダンス][lnk-customize]」を参照してください。

TechNet のブログ投稿「[IoT Suite - Under The Hood - Predictive Maintenance (IoT スイート - 内部のしくみ - 予測的なメンテナンス)](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx)」では、予測的なメンテナンスの構成済みソリューションについて詳しく説明されています。

IoT Suite の事前構成済みのソリューションの他の機能について学習できます。

- [IoT スイートに関してよく寄せられる質問][lnk-faq]
- [IoT の徹底的なセキュリティ][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

<!---HONumber=AcomDC_0817_2016-->