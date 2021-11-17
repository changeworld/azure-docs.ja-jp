---
title: Microsoft Azure IoT Hub を使用したデバイス管理の概要
description: 'Azure IoT Hub でのデバイス管理の概要: エンタープライズ デバイスのライフサイクルとデバイス管理パターン (再起動、出荷時の設定にリセット、ファームウェア更新、構成、デバイス ツイン、クエリ、ジョブ、脅威検出など)。'
author: anastasia-ms
ms.service: iot-hub
services: iot-hub
ms.author: v-stharr
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 7e21115bd1f55d79b1ba09b91cea05576a74dd75
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312536"
---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT Hub を使用したデバイス管理の概要

Azure IoT Hub には、デバイスやバックエンドの開発者が堅牢なデバイス管理ソリューションを構築するために使用できる機能や拡張モデルが用意されています。 デバイスは、リソースの制約の大きいセンサーをはじめ、専用マイクロコントローラー、デバイス グループの通信をルーティングする強力なゲートウェイなど、多岐にわたります。  また、ユース ケースや IoT オペレーターの要件は業界によってかなり異なります。  このような違いがありながら、IoT Hub によるデバイス管理で提供される機能、パターン、およびコード ライブラリは、多様なデバイスとエンド ユーザーに対応することができます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

優れたエンタープライズ IoT ソリューションを構築するために不可欠な要素は、オペレーターがそのデバイス コレクションを継続的に管理するための手法について、戦略を定めることです。 IoT のオペレーターには、シンプルで信頼性が高く、かつ、より戦略的な観点からその仕事に取り組むことのできるツールとアプリケーションが必要です。 この記事では、次について説明します。

* デバイス管理に対する Azure IoT Hub のアプローチの概要。
* 一般的なデバイス管理の原則の説明。
* デバイスのライフサイクルの説明。
* 一般的なデバイス管理パターンの概要。

## <a name="device-lifecycle"></a>デバイスのライフサイクル

一般的なデバイス管理ステージは、ほとんどのエンタープライズ IoT プロジェクトに共通しています。 Azure IoT では、デバイスのライフサイクルは 5 つのステージで構成されています。

![Azure IoT デバイス のライフサイクルの 5 つのフェーズ: 計画、プロビジョニング、構成、監視、使用中止](./media/iot-hub-device-management-overview/image5.png)

この 5 つの各ステージにデバイス オペレーター要件がいくつかあり、完全なソリューションを提供するには、これを満たさなければなりません。

* **計画**: オペレーターは、デバイスのメタデータ体系を作成できます。これらのプロパティを使用することで、簡単かつ正確に一連のデバイスを照会して一括管理操作の対象にすることができます。 デバイス ツインを使用すると、このデバイスのメタデータをタグやプロパティの形式で保存できます。
  
    *参考資料*: 
  * [デバイス ツインを使ってみる](iot-hub-node-node-twin-getstarted.md)
  * [デバイス ツインを理解する](iot-hub-devguide-device-twins.md)
  * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
  * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)

* **プロビジョニング**: IoT Hub に対して新しいデバイスを安全にプロビジョニングし、デバイスの機能をオペレーターがすぐに検出できるようにします。  柔軟性に優れたデバイス ID と資格情報を作成するため、IoT Hub ID レジストリを使用します。さらに、ジョブを使用してこの操作を一括で実行します。 デバイス ツインのデバイス プロパティを使ってその機能や状態を報告できるように、デバイスを構築します。
  
    *参考資料*: 
    * [デバイス ID を管理する](iot-hub-devguide-identity-registry.md)
    * [デバイス ID の一括管理](iot-hub-bulk-identity-mgmt.md)
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)

* **構成**: 正常性とセキュリティを維持すると共に、デバイスに対する一括構成変更とファームウェア更新を効率化します。 こうしたデバイス管理操作は、Desired プロパティ、またはダイレクト メソッドとブロードキャスト ジョブを使用して一括で実行します。
  
    *参考資料*:
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [多数の IoT デバイスの構成と監視](./iot-hub-automatic-device-management.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)

* **監視**: デバイス コレクション全体の正常性や、進行中の操作の状態を監視して、対処が必要な問題があればオペレーターに通知します。  デバイス ツインを適用して、デバイスが稼働状況と更新操作の状態をリアルタイムで報告できるようにします。 デバイス ツインのクエリを使用して緊急を要する問題を報告するための強力なダッシュボード レポートを作成します。 完全なオンプレミス、クラウド接続、ハイブリッドなど、複数の展開オプションを使用して、IoT 環境を脅威から保護します。
  
    *参考資料*: 
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)
    * [多数の IoT デバイスの構成と監視](./iot-hub-automatic-device-management.md)
    * [包括的な脅威検出を提供する組織向け Microsoft Defender for IoT](../defender-for-iot/organizations/overview.md)
    * [IoT ソリューション内のデバイス構成のベスト プラクティス](iot-hub-configuration-best-practices.md)

* **使用中止**: 故障後やアップグレード サイクルの終了後、またはサービス有効期間の経過後に、デバイスを交換またはデバイスの使用を停止します。  デバイス ツインを使用して、物理デバイスが交換されている場合は、デバイス情報を保持します。使用中止になっている場合はアーカイブされます。 IoT Hub ID レジストリを使用して、デバイス ID と資格情報を安全に失効します。
  
    *参考資料*: 
    * [デバイス ツインのプロパティの使用方法](tutorial-device-twins.md)
    * [デバイス ID を管理する](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>デバイス管理パターン

以下に示したのは、IoT Hub によって実現される一連のデバイス管理パターンです。 [デバイス管理のチュートリアル](iot-hub-node-node-device-management-get-started.md)では、このパターンを実際のシナリオに合わせて拡張する方法と、こうしたコア テンプレートに基づいて新しいパターンをデザインする方法について詳しく説明しています。

* **再起動**: バックエンド アプリによって再起動が開始されたことが、ダイレクト メソッドでデバイスに伝えられます。  デバイスは、報告されるプロパティを使用して、その再起動状態を更新します。
  
    ![デバイス管理の再起動パターンを示す図](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **出荷時の設定にリセット**: バックエンド アプリによって出荷時の設定へのリセットが開始されたことが、ダイレクト メソッドでデバイスに伝えられます。 デバイスは、報告されるプロパティを使用して、そのデバイスの出荷時の設定へのリセット状態を更新します。
  
    ![デバイス管理の出荷時の設定へのリセット パターンを示す図](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **構成** - バックエンド アプリでは、必要なプロパティを使用して、デバイス上で実行されているソフトウェアが構成されます。 デバイスは、報告されるプロパティを使用して、その構成状態を更新します。
  
    ![デバイス管理の構成パターンを示す図](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **進行状況とステータスの報告**: ソリューション バックエンドによって、一連のデバイス対してデバイス ツイン クエリが実行され、デバイス上で実行されている操作のステータスと進行状況が報告されます。
  
    ![デバイス管理の進行状況とステータスの報告パターンを示す図](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="device-updates"></a>デバイスの更新

[Device Update for IoT Hub](../iot-hub-device-update/understand-device-update.md) は、小さなセンサーからゲートウェイレベルのデバイスまで、あらゆるものを対象にした Over-the-Air 更新を公開、配布、管理するために顧客が使用できる包括的なプラットフォームです。 顧客は、Device Update for IoT Hub を使用することにより、独自の更新プラットフォームを構築するための開発および保守の追加コストをかけずに、セキュリティの脅威に迅速に対応したり、事業目標を達成するための機能を展開したりすることができます。

Device Update for IoT Hub と Azure IoT Hub を統合することにより、更新プログラムの展開の最適化および操作の効率化を実現できます。 Azure IoT Edge によって範囲が拡大するため、事実上すべてのデバイスに接続できるクラウド ホスト型ソリューションを提供できます。 また、Linux や Azure RTOS (リアルタイム オペレーティング システム) を含む広範な IoT オペレーティング システムによってサポートされており、オープンソースを通じて拡張できます。 機能の一部を以下に示します。

* Azure IoT Edge のホスト レベル コンポーネントを含むエッジ デバイスの更新のサポート
* Azure IoT Hub と統合された更新管理 UX
* デバイスのグループ化と更新スケジュール制御による段階的な更新のロールアウト
* 自動化とカスタム ポータル エクスペリエンスを可能にするためのプログラム API
* 更新のコンプライアンスとステータスを異種デバイス フリート全体にわたって一目で把握できるビュー
* 回復性があるデバイス更新 (A/B) のサポートによってシームレスなロールバックを実現
* 組み込みの Microsoft 接続キャッシュおよび Azure IoT Edge との統合による、コンテンツ キャッシングと切断されたデバイスのサポート (構成が入れ子になっているデバイスを含む)
* Azure.com ポータルから利用可能なサブスクリプションおよびロールベースのアクセス制御
* クラウドからエッジまでの包括的なセキュリティ機能とプライバシー制御

詳細については、[Device Update for IoT Hub](../iot-hub-device-update/index.yml) に関するページを参照してください。

## <a name="next-steps"></a>次の手順

IoT Hub でデバイス管理用に提供される機能、パターン、コード ライブラリを使用することで、デバイスのライフサイクル ステージごとのエンタープライズ IoT オペレーター要件を満たす IoT アプリケーションを作成できます。

IoT Hub のデバイス管理機能についての学習を続けるには、[デバイス管理の概要](iot-hub-node-node-device-management-get-started.md)に関するチュートリアルをご覧ください。
