<properties
	pageTitle="Microsoft Azure IoT Suite の概要 | Microsoft Azure"
	description="パッケージ化と構成済みのソリューションを含む、Azure IoT Suite の概要を説明します。"
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Azure IoT Suite の概要

Microsoft では、Azure IoT サービスによって幅広い機能を提供しています。これらのサービスは、デバイスからのデータの収集、進行中のデータ ストリームの分析、大規模なデータ セットの保存とクエリ、リアルタイム データと履歴データの両方の視覚化、重要なバックオフィス システムの統合に関して、企業レベルの機能を備えています。Azure IoT Suite は、一般的に利用される一連のサービスと、お客様が目標を達成するまでの時間を短縮できる拡張機能をパッケージ化しています。これらの拡張機能には、最も一般的なソリューション パターンの基本実装を行う構成済みのソリューションが含まれています。お客様は、IoT ソフトウェア開発キット (SDK) と組み合わせることで、構成済みのソリューションのカスタマイズや新しいソリューションを開発するためのサンプルとしての利用を容易に実行できます。

## Azure IoT Suite の Azure IoT サービス

Azure IoT Suite の中核となるものは IoT Hub サービスです。このサービスでは、デバイスとクラウドの間のメッセージング機能を提供します。クラウドやその他の主要な IoT Suite サービスへのゲートウェイとして機能します。

インモーション データ分析は Azure Stream Analytics から提供されます。IoT Suite は、受信テレメトリ メッセージの処理、集計、イベントの削除にこのサービスを利用します。また、デバイス メタデータやコマンドの応答に使用できる情報メッセージの処理に使用されます。

データ ストレージ機能は Azure Storage と Azure Document DB の組み合わせにより有効になります。Azure Storage では、保存や将来の分析のためテレメトリの BLOB ストレージを有効にできます。Document DB は、デバイス メタデータを管理するために、半構造化されたデータにインデックスを付けて保存するために使用されます。異なるデバイスで異なるコンテンツを保持できるため、異種デバイスの管理が可能になります。

データの視覚化は Azure Websites と Microsoft Power BI の組み合わせによって提供されます。Power BI の柔軟性により、お客様は IoT Suite データから独自の対話型ダッシュボードをすばやく構築できます。

アーキテクチャとこれらのサービスの使用方法に関する詳細は、「[Microsoft Azure とモノのインターネット (IoT)](iot-suite-what-is-azure-iot.md)」という記事にあります。

## 構成済みのソリューション

構成済みのソリューションが Azure IoT Suite に含まれており、お客様は Azure IoT Suite をすぐに開始し、可能なシナリオを試すことができます。

使用可能な最初の構成済みのソリューションは[リモート監視](iot-suite-what-are-preconfigured-solutions.md)です。

<!---HONumber=Oct15_HO3-->