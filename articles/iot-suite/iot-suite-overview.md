<properties
	pageTitle="Microsoft Azure IoT Suite の概要 | Microsoft Azure"
	description="パッケージ化と構成済みのソリューションを含む、Azure IoT Suite の概要を説明します。"
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Azure IoT Suite の概要

Azure IoT サービスは、さまざまな機能を提供しています。このエンタープライズ クラスのサービスを使用すると、次の操作を実行できます。

- デバイスからデータを収集する
- インモーション データ ストリームを分析する
- 大規模なデータ セットを格納して照会する
- リアルタイム データと履歴データを表示する
- バックオフィス システムと統合する

Azure IoT Suite には、一般的に利用されている Azure サービスとカスタム拡張機能が構成済みソリューションとしてパッケージ化されています。構成済みソリューションは、一般的な IoT ソリューション パターンの基本実装です。このソリューションを使用して、IoT ソリューションを提供する時間を短縮できます。[IoT ソフトウェア開発キット][lnk-sdks]を使用して独自の要件に合わせて構成済みソリューションを簡単にカスタマイズしたり、新しいソリューションを開発するときの例として利用したりすることができます。

次のビデオで、Azure IoT Suite の概要について説明します。

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT Suite の Azure IoT サービス

Azure IoT Suite の中核となるものは [Azure IoT Hub][lnk-iot-hub] サービスです。このサービスは、デバイスからクラウドへ、およびクライアントからデバイスへのメッセージング機能を提供し、クラウドや他の主要な IoT Suite サービスに対するゲートウェイとして機能します。

[Azure Stream Analytics][lnk-asa] には、インモーション データ分析機能があります。IoT Suite は、このサービスを利用して、受信テレメトリを処理し、集計を実行してイベントを検出します。また、事前構成されたソリューションは、ストリーム分析を使用して、メタデータやデバイスからのコマンド応答などのデータを含む情報メッセージも処理します。

[Azure Storage][lnk-azure-storage] と [Azure DocumentDB][lnk-document-db] には、データ ストレージ機能があります。構成済みソリューションでは、BLOB ストレージを使用してテレメトリを保存し、分析に使用することができます。構成済みソリューションでは、DocumentDB の半構造化データ機能でインデックス化されたストレージを使用します。その結果、ストレージ要件が異なる複数種類のデバイスをソリューションで管理できるようになります。

[Azure Web Apps][lnk-web-apps] と [Microsoft Power BI][lnk-power-bi] には、データ仮想化機能があります。Power BI は柔軟なので、IoT Suite データを使用する独自の対話型ダッシュボードをすばやく構築できます。

一般的な IoT ソリューションのアーキテクチャの概要については、「[Microsoft Azure とモノのインターネット (IoT)][iot-suite-what-is-azure-iot]」を参照してください。

## 構成済みのソリューション

IoT Suite には構成済みソリューションが含まれているので、Azure IoT Suite で実現できる一般的な IoT シナリオをすぐに始めて、調べることができます。構成済みソリューションを Azure サブスクリプションにデプロイし、完全なエンドツーエンドの IoT ソリューションを実行することができます。

## 次のステップ

IoT Suite の構成済みソリューションについては、「[Azure IoT の構成済みソリューションとは][lnk-what-are-preconfig]」を参照してください。

構成済みソリューションのいずれかを初めて使用する方法については、[IoT の構成済みソリューションの使用に関するページ][lnk-preconfig-start]を参照してください。

Azure IoT Hub サービスについては、「[IoT Hub のドキュメント][lnk-iot-hub]」を参照してください。


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0128_2016-->