<properties
 pageTitle="デバイス管理の概要 | Microsoft Azure"
 description="Azure IoT Hub デバイス管理の概要"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/16/2016"
 ms.author="bzurcher"/>



# Azure IoT Hub デバイス管理の概要 (プレビュー)

## Azure IoT によるデバイス管理手法

Azure IoT Hub デバイス管理は、IoT におけるデバイスとプロトコルの多様性に対応するために、デバイスやバックエンドで IoT デバイス管理を活用するための機能と拡張モデルを備えています。IoT におけるデバイスは、リソースの制約の大きいセンサーをはじめ、専用マイクロコントローラー、他のデバイスやプロトコルに対応したより強力なゲートウェイなど、多岐にわたります。また、ひとくちに IoT ソリューションといっても、さまざまな分野や用途があり、オペレーターのユース ケースは分野ごとに異なります。IoT ソリューションは、そうした多様なデバイスとユーザーのために、IoT Hub デバイス管理の機能、パターン、コード ライブラリを最大限に活用してデバイス管理を実現します。

## はじめに

優れた IoT ソリューションを構築するために不可欠な要素は、オペレーターがそのデバイス群を継続的に管理するための手法について戦略を定めることです。IoT のオペレーターには、シンプルで信頼性が高く、かつその仕事に対し、より戦略的な観点から取り組むことのできるツールとアプリケーションが必要です。Azure IoT Hub には、最も重要なデバイス管理パターンを省力化する IoT アプリケーションを構築するためのビルディング ブロックが備わっています。

デバイスが IoT Hub の管理下にあると見なされるのは、"デバイス管理エージェント" と呼ばれる、デバイスを安全にクラウドに接続する単純なアプリケーションを実行する場合です。このエージェントのコードによって、オペレーターが遠隔のアプリケーション側からデバイスの状態を確認し、ネットワークの構成変更やファームウェア更新プログラムのデプロイといった管理操作を実行することができます。

## IoT デバイス管理の原則

IoT の管理には特有の課題が伴います。IoT デバイス管理に関して、ソリューションは以下の原則に対応する必要があります。

![][img-dm_principles]

- **スケールとオートメーション**: IoT には、日々の作業を自動化して比較的少人数の運用スタッフで何百万台というデバイスを管理できるシンプルなツールが必要です。日常的な業務としてオペレーターは、多数のデバイスを遠隔から一括で操作し、直接の対応が必要な問題が発生した場合にだけ通知を受け取りたいと考えます。

- **開放性と互換性**: IoT デバイスのエコシステムはきわめて多様です。数多くのデバイス クラス、プラットフォーム、プロトコルに管理ツールを適合させる必要があります。オペレーターは、制約が最も厳しいシングル プロセスの組み込みチップから、フル機能の高性能コンピューターまで、あらゆるデバイスをサポートできなければなりません。

- **コンテキスト認識**: IoT の環境は動的で絶えず変化しています。サービスの信頼性は最優先課題です。デバイス管理操作には、SLA の保守ウィンドウ、ネットワークと電源の状態、使用中のコンディション、デバイスの位置情報を考慮に入れ、メンテナンスのダウンタイムで重要業務に支障が生じたり、危険な状況を招いたりすることを確実に避ける必要があります。

- **さまざまなスタッフへの情報提供**: IoT 運用スタッフがそれぞれに抱えるワークフローやプロセスに対応することが不可欠となります。また運用スタッフは、社内 IT 部門に課せられた制約に適合しながら、関連するデバイス運用情報を監督者やその他の管理職にわかりやすく伝える必要があります。

## IoT デバイスのライフサイクル 

IoT プロジェクトはきわめて多様ですが、デバイスを管理するうえでの共通するパターンはあります。Azure IoT では、これらのパターンが IoT デバイス ライフサイクルの中で明確化されています。次に示すように、このライフスタイルは、はっきりと区別された 5 つの段階から成ります。

![][img-device_lifecycle]

1. **計画**: オペレーターは、デバイスのプロパティ体系を作成できます。これらのプロパティを使用することで、簡単かつ正確に一連のデバイスを照会して一括管理操作の対象にすることができます。

    *関連するビルディング ブロック*: [デバイス ツインの概要][lnk-twins-getstarted]、[ツイン プロパティの使用方法][lnk-twin-properties]

2. **プロビジョニング**: IoT Hub に対して新しいデバイスを安全に認証し、デバイスの機能と現在の状態をオペレーターがすぐに検出できるようにします。

    *関連するビルディング ブロック*: [IoT Hub の使用][lnk-hub-getstarted]、[ツイン プロパティの使用方法][lnk-twin-properties]

3. **構成**: 正常性とセキュリティを維持すると共に、デバイスに対する一括構成変更とファームウェア更新を効率化します。

    *関連するビルディング ブロック*: [ツイン プロパティの使用方法][lnk-twin-properties]、[C2D のメソッド][lnk-c2d-methods]、[ジョブのスケジュール/ブロードキャスト][lnk-jobs]

4. **監視**: デバイス群全体の正常性を監視すると共に、進行中の更新プログラムの展開状態を監視して、対処が必要な問題があればオペレーターに通知します。

    *関連するビルディング ブロック*: [ツイン プロパティの使用方法][lnk-twin-properties]

5. **使用中止**: 故障後やアップグレード サイクルの終了後、またはサービス有効期間の経過後に、デバイスを交換またはデバイスの使用を停止します。

    *関連するビルディング ブロック*:
    
## IoT Hub のデバイス管理パターン

以下に示したのは、IoT Hub によって実現される一連の (初期) デバイス管理パターンです。[チュートリアル][lnk-get-started]でも触れたように、これらのパターンは実際のシナリオに合わせて拡張できるほか、こうした核となるパターンをベースとして新しいパターンをデザインすることができます。

1. **再起動** - 再起動が開始されたことをバックエンド アプリケーションが D2C メソッドを通じてデバイスに伝えます。デバイスは、デバイス ツインの Reported プロパティを使用して、その再起動ステータスを更新します。

    ![][img-reboot_pattern]

2. **出荷時の設定にリセット** - 出荷時の設定へのリセットが開始されたことをバックエンド アプリケーションが D2C メソッドを通じてデバイスに伝えます。デバイスは、デバイス ツインの Reported プロパティを使用して、出荷時の設定へのリセット ステータスを更新します。

    ![][img-facreset_pattern]

3. **構成** - バックエンド アプリケーションが、デバイス ツインの Desired プロパティを使用して、デバイス上で実行されているソフトウェアを構成します。デバイスは、デバイス ツインの Reported プロパティを使用して、その構成ステータスを更新します。

    ![][img-config_pattern]

4. **ファームウェア更新** - ファームウェア更新が開始されたことをバックエンド アプリケーションが D2C メソッドを通じてデバイスに伝えます。デバイスは、複数のステップから成るプロセスを開始してファームウェア パッケージをダウンロードし、それを適用して、最後に IoT Hub サービスに再接続します。この複数のステップから成るプロセスの間、デバイスは、デバイス ツインの Reported プロパティを使用して、デバイスの進行状況とステータスを更新します。

    ![][img-fwupdate_pattern]

5. **進行状況とステータスの報告** - アプリケーション バックエンドは、デバイス群に対してデバイス ツイン クエリを実行し、デバイス上で実行されている操作のステータスと進行状況を報告します。

    ![][img-report_progress_pattern]

## 次のステップ

開発者は、Azure IoT Hub が備えるビルディング ブロックを使用して IoT アプリケーションを作成することで、デバイス ライフサイクルの各ステージにおいて IoT オペレーターが抱えている固有の要件を満たすことができます。

Azure IoT Hub デバイス管理機能について学習を継続するには、[Azure IoT Hub デバイス管理の概要][lnk-get-started]のチュートリアルをご覧ください。

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->