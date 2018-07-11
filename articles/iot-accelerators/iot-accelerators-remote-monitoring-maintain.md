---
title: リモート監視ソリューションでアラートを使用し、デバイスの問題を修正する - Azure | Microsoft Docs
description: このチュートリアルでは、アラートを使用して、リモート監視ソリューション アクセラレータに接続されているデバイスの問題を特定し、修正する方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/18/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 9607705220450b30d2ffaf0f2be9fa2a5664b879
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081790"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>トラブルシューティングを行ってデバイスの問題を修復する

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されている IoT デバイスの問題を特定し、修正します。 ソリューション アクセラレータ ダッシュボードでアラートを使用して問題を特定した後、リモート ジョブを実行して問題を修正します。

Contoso は、フィールド内の新しい**プロトタイプ** デバイスをテストしています。 Contoso の運用者であるあなたは、テスト中に**プロトタイプ** デバイスがダッシュボード上で予期せぬ温度アラートをトリガーしていることに気付きます。 今すぐこの異常のある**プロトタイプ** デバイスの動作を調査し、問題を解決する必要があります。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * デバイスからのアラートを調査する
> * デバイスの問題を解決する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューション アクセラレータのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューション アクセラレータをデプロイしていない場合は、クイック スタート「[クラウドベースのリモート監視ソリューションのデプロイ](quickstart-remote-monitoring-deploy.md)」を完了する必要があります。

## <a name="investigate-an-alert"></a>アラートの調査

**[ダッシュボード]** ページに、**プロトタイプ** デバイスに関連付けられたルールから生じる予期しない温度アラートがあることに気付きます。

[![ダッシュボードに表示されているアラート](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

問題をさらに調査するため、アラートの横にある **[Explore Alert] (アラートの確認)** オプションを選択します。

[![ダッシュボードでアラートを確認する](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

アラートの詳細ビューには、以下が表示されます。

* アラートがトリガーされた時間
* アラートに関連付けられているデバイスに関する状態の情報
* アラートに関連付けられているデバイスのテレメトリ

[![アラートの詳細](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

アラートを確認するには、**発生したアラート**をすべて選択し、**[Acknowledge]\(確認\)** を選択します。 このアクションにより、他のオペレーターは、アラートが確認済みであり、対応中であることを知ることができます。

[![アラートを確認する](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

アラートを確認すると、発生の状態は **[承認済み]** に変わります。

一覧では、温度アラートを発生させた**プロトタイプ** デバイスを表示できます。

[![アラートの原因となったデバイスを一覧表示する](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>問題の解決

**プロトタイプ** デバイスの問題を解決するには、デバイスで **DecreaseTemperature** メソッドを呼び出す必要があります。

デバイスに対処するには、デバイスの一覧でデバイスを選択して、**[ジョブ]** を選択します。 **プロトタイプ** デバイス モデルでは、デバイスでサポートする必要のある 6 つのメソッドを指定します。

[![デバイスでサポートされているメソッドを表示する](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

**DecreaseTemperature** を選択して、ジョブの名前を **DecreaseTemperature** に設定します。 **[適用]** を選択します。

[![温度を低下させるジョブを作成する](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

ジョブの状態を追跡するには、**[View job status]\(ジョブの状態の表示\)** をクリックします。 **[ジョブ]** ビューを使用して、ソリューション内のすべてのジョブとメソッドの呼び出しを追跡します。

[![温度を低下させるジョブを監視する](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

**ダッシュボード** ページでテレメトリを表示すると、デバイスの温度が低下していることを確認できます。

[![温度の低下を確認する](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アラートを使用してデバイスの問題を特定する方法と、対象のデバイスを操作して問題を解決する方法について説明しました。 物理デバイスをソリューション アクセラレータに接続する方法を学習するには、ハウツー記事に進んでください。

デバイスの問題を管理する方法を学習した後は、次のステップとして、[デバイスをリモート監視ソリューション アクセラレータに接続する](iot-accelerators-connecting-devices.md)方法を学習することをお勧めします。
