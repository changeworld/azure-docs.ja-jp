---
title: "リモート監視ソリューションでのデバイスのトラブルシューティング - Azure | Microsoft Docs"
description: "このチュートリアルでは、トラブルシューティングを行ってリモート監視ソリューションでのデバイスの問題を修復する方法を示します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ac284126c2c940caf97fb95bd46234de49b1f678
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="troubleshoot-and-remediate-device-issues"></a>トラブルシューティングを行ってデバイスの問題を修復する

このチュートリアルでは、トラブルシューティングを行ってデバイスの問題を修復するために、ソリューションの **[メンテナンス]** ページを使用する方法を示します。 これらの機能を紹介するために、このチュートリアルでは Contoso IoT アプリケーションのシナリオを使用します。

Contoso は、フィールド内の新しい**プロトタイプ** デバイスをテストしています。 Contoso の運用者であるあなたは、テスト中に**プロトタイプ** デバイスがダッシュボード上で予期せぬ温度アラームをトリガーしていることに気付きます。 今すぐこの異常のある**プロトタイプ** デバイスの動作を調査する必要があります。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * **[メンテナンス]** ページを使用してアラームを調査する
> * デバイス メソッドを呼び出して問題を修復する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにデプロイされたリモート監視ソリューションのインスタンスが必要です。

リモート監視ソリューションをまだデプロイしていない場合は、「[Deploy the remote monitoring preconfigured solution (リモート監視の事前構成済みソリューションのデプロイ)](iot-suite-remote-monitoring-deploy.md)」のチュートリアルを完了しておく必要があります。

## <a name="use-the-maintenance-dashboard"></a>メンテナンス ダッシュボードの使用

**[ダッシュボード]** ページに、**プロトタイプ** デバイスに関連付けられたルールから生じる予期しない温度アラームがあります。

![ダッシュボードに表示されているアラーム](media/iot-suite-remote-monitoring-maintain/dashboardalarm.png)

問題をさらに調査するため、アラームの横にある **[Explore Alarm]\(アラームの確認\)** オプションを選択します。

![ダッシュボードのアラームを確認する](media/iot-suite-remote-monitoring-maintain/dashboardexplorealarm.png)

**[メンテナンス]** ページにアラームの一覧が表示されます。

![メンテナンス ページのアラームの一覧](media/iot-suite-remote-monitoring-maintain/maintenancealarms.png)

アラームの詳細を表示するには、**[アラーム]** 一覧からアラームを選択します。 詳細ビューでは次のものが表示されます。

* アラームがトリガーされた時間
* アラームに関連付けられているデバイスに関する状態の情報
* アラームに関連付けられているデバイスのテレメトリ

![アラームの詳細](media/iot-suite-remote-monitoring-maintain/maintenancealarmdetail.png)

アラームを確認するには、**[Alarm occurrences]\(発生したアラーム\)**、**[Acknowledge]\(確認\)** を選択します。 このアクションにより、他の運用者は、アラームが確認済みであり、対応中であることを知ることができます。

![アラームを確認する](media/iot-suite-remote-monitoring-maintain/maintenanceacknowledge.png)

一覧では、温度アラームを発生させた**プロトタイプ** デバイスを表示できます。

![アラームの原因となったデバイスを一覧表示する](media/iot-suite-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>問題を修復する

**プロトタイプ** デバイスの問題を修復するには、デバイスで **DecreaseTemperature** メソッドを呼び出す必要があります。

デバイスでの動作を設定するには、デバイスの一覧でデバイスを選択して、**[スケジュール]** を選択します。 **エンジン** デバイス モデルでは、デバイスでサポートする必要のある 3 つのメソッドを指定します。

![デバイスでサポートされているメソッドを表示する](media/iot-suite-remote-monitoring-maintain/maintenancemethods.png)

**DecreaseTemperature** を選択して、ジョブの名前を **DecreaseTemperature** に設定します。 **[適用]** を選択します。

![温度を低下させるジョブを作成する](media/iot-suite-remote-monitoring-maintain/maintenancecreatejob.png)

**[メンテナンス]** ページで ジョブの状態を追跡するには、**[システムの状態]** を選択します。 **[システムの状態]** ビューを使用して、ソリューション内のすべてのジョブとメソッドの呼び出しを追跡します。

![温度を低下させるジョブを監視する](media/iot-suite-remote-monitoring-maintain/maintenancerunningjob.png)

特定のジョブまたはメソッドの呼び出しの詳細を表示するには、**[システムの状態]** ビューの一覧で選択します。

![ジョブの詳細を表示する](media/iot-suite-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * **[メンテナンス]** ページを使用してアラームを調査する
> * デバイス メソッドを呼び出して問題を修復する

ここではデバイスの問題の管理方法を説明しました。推奨する次のステップは、[シミュレートされたデバイスを使用したソリューションのテスト](iot-suite-remote-monitoring-test.md)の方法の学習です。

<!-- Next tutorials in the sequence -->