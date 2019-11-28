---
title: 変換の概要
description: 変換する方法について説明します
author: viv-liu
ms.author: viviali
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 270017e23bef4be439dbe70bf1845f75333c5311
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894387"
---
# <a name="transform-your-iot-data-preview-features"></a>IoT データを変換する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

IoT Central ではアプリケーション プラットフォームとして、IoT データを、アクションにつながる結果をもたらすビジネス分析情報に変換する際に役立ついくつかの重要なファセットを提供します。 IoT Central は、IoT データを外部システムに拡張して、基幹業務アプリケーションやカスタム アプリケーションと統合する方法を提供します。 モバイル サービスを介して技術者に通知を行う規則を作成することによって、デバイスの正常性と運用状況を監視できます。 未加工のテレメトリ データを Azure のサービスにエクスポートすることにより、カスタム分析や機械学習を使用して固有のビジネス分析情報を生成できます。 パブリック API を使用して、デバイスを監視および制御し、IoT Central アプリを管理するサービスとツールを作成できます。 

![IoT Central での変換の概要](media/overview-iot-central-transform/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>規則を使用してデバイスの正常性と運用状況を監視する
マシンが接続されてデータが送信されるようになると、問題が発生しているマシンやエラー メッセージを送信しているマシンを特定できます。これにより、それらが正常に実行されるよう最小限のダウンタイムで修正できます。 IoT Central アプリでは、デバイスから届くテレメトリを監視して、しきい値を超えた場合または特定のイベント メッセージが送信されたときにアラートを生成する規則を作成できます。 規則に対してメール アクションや Webhook などのアクションを構成して、適切なユーザーと適切なダウンストリーム システムに通知することができます。 詳細については、こちらを参照してください。

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>エクスポートされたデータに対してカスタムの分析と処理を実行する
カスタム分析パイプラインを構築して未加工の IoT テレメトリを処理し、最終的な結果を格納することにより、工場現場における機械の効率性の傾向を判断したり、将来の電力使用量を予測したりするなど、高度にカスタマイズされたビジネス分析情報を生成できます。 IoT Central アプリにデータ エクスポートを作成し、テレメトリ、デバイスのプロパティとライフサイクル、デバイス テンプレートの変更情報を他の Azure サービスにエクスポートすることができます。これにより、お客様にとって最も有用なツールでデータを分析、格納、視覚化できます。 データのエクスポートの詳細については、こちらを参照してください。

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>API を使用してカスタムの IoT ソリューションと統合を構築する
デバイスをリモートで制御したり、新しいデバイスを設定したりできるモバイル コンパニオン アプリのような IoT ソリューションを構築できます。また、既存の基幹業務アプリケーションとのカスタム統合を行って、お客様のビジネスに合わせて調整された IoT デバイスやデータとやり取りすることもできます。 IoT Central は、デバイスのモデル化、オンボード、管理、およびデータ アクセスを行うためのバックボーンとして使用できます。 パブリック API の詳細については、こちらの学習モジュールを参照してください。
