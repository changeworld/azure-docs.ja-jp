---
title: リモート監視ソリューションでのデバイス管理 - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューションに接続されているデバイスを管理する方法を示します。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 3ad6de2a0ebcd257ca90ea3c5c69988d4c1afd7a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
---
# <a name="manage-and-configure-your-devices"></a>デバイスの管理と構成

このチュートリアルでは、リモート監視ソリューションのデバイス管理機能を示します。 これらの機能を紹介するために、このチュートリアルでは Contoso IoT アプリケーションのシナリオを使用します。

Contoso は自社の施設の 1 つを拡大して生産量を増やすために、新しい機械を注文しました。 新しい機械が納品されるのを待つ間に、あなたはソリューションの動作を検証するためのシミュレーションを実施したいと考えています。 あなたはオペレーターとして、リモート監視ソリューションでデバイスを管理および構成することを希望しています。

デバイスを管理および構成する拡張可能な方法を提供するために、リモート監視ソリューションでは[ジョブ](../iot-hub/iot-hub-devguide-jobs.md)や[ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md)などの IoT Hub 機能を使用します。 デバイスの開発者が物理デバイスでメソッドを実装する方法については、「[リモート監視ソリューション アクセラレータをカスタマイズする](iot-accelerators-remote-monitoring-customize.md)」を参照してください。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * シミュレートされたデバイスをプロビジョニングする。
> * シミュレートされたデバイスをテストする。
> * ソリューションからデバイス メソッドを呼び出す。
> * デバイスを再構成する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューションのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューションをデプロイしていない場合は、「[リモート監視ソリューション アクセラレータをデプロイする](iot-accelerators-remote-monitoring-deploy.md)」チュートリアルを実行する必要があります。

## <a name="add-a-simulated-device"></a>シミュレートされたデバイスの追加

ソリューションの **[デバイス]** ページに移動して、**[+ 新規デバイス]** を選びます。 **[新規デバイス]** パネルで、**[シミュレート済み]** を選択します。

![シミュレートされたデバイスのプロビジョニング](./media/iot-accelerators-remote-monitoring-manage/devicesprovision.png)

プロビジョニングするデバイスの数を **1** のままにします。 デバイス モデルとして **[Faulty Engine]\(問題があるエンジン\)** を選び、**[適用]** を選んでシミュレートされたデバイスを作成します。

![シミュレートされたエンジン デバイスのプロビジョニング](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine.png)

*物理*デバイスをプロビジョニングする方法については、「[Connect your device to the remote monitoring solution accelerator](iot-accelerators-connecting-devices-node.md)」 (デバイスをリモート監視ソリューション アクセラレータに接続する) を参照してください。

## <a name="test-the-simulated-device"></a>シミュレートされたデバイスのテスト

シミュレートされた新しいデバイスの詳細を表示するには、**[デバイス]** ページにあるデバイスの一覧でそのデバイスを選択します。 デバイスの情報が **[Device detail]\(デバイスの詳細\)** パネルに表示されます。

![新しくシミュレートされたエンジン デバイスの表示](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew.png)

**[Device detail]\(デバイスの詳細\)** で、新しいデバイスがテレメトリを送信していることを確認します。 デバイスから別のテレメトリ ストリームを表示するには、**vibration** などのテレメトリ名を選択します。

![表示するテレメトリ ストリームの選択](./media/iot-accelerators-remote-monitoring-manage/devicesvibration.png)

**[Device detail]\(デバイスの詳細\)** パネルには、デバイスに関するその他の情報 (タグ値、デバイスがサポートするメソッド、デバイスから報告されるプロパティなど) が表示されます。

詳細な診断を確認するには、下方にスクロールして **[診断]** を表示します。

## <a name="act-on-a-device"></a>デバイスでの動作

1 つ以上のデバイスに対処するには、デバイスの一覧でデバイスを選択して、**[ジョブ]** を選択します。 **エンジン** デバイス モデルでは、デバイスでサポートする必要のある 3 つのメソッドを指定します。

![エンジンのメソッド](./media/iot-accelerators-remote-monitoring-manage/devicesmethods.png)

**[FillTank]** を選択し、ジョブ名を **[FillEngineTank]** に設定して、**[適用]** を選択します。

![restart メソッドのスケジュール](./media/iot-accelerators-remote-monitoring-manage/devicesrestartengine.png)

**[メンテナンス]** ページでジョブの状態を追跡するには、**[ジョブ]** を選択します。

![スケジュール ジョブの監視](./media/iot-accelerators-remote-monitoring-manage/maintenancerestart.png)

### <a name="methods-in-other-devices"></a>その他のデバイス内のメソッド

別の種類のシミュレートされたデバイスを確認すると、デバイスの種類によってサポートされるメソッドが異なることがわかります。 物理デバイスを使用した展開では、デバイスでサポートする必要のあるメソッドをデバイス モデルで指定します。 通常、メソッド呼び出しに対応してデバイスを動作させるコードの開発は、デバイス開発者が担当します。

複数のデバイスで実行するメソッドのスケジュールを設定するために、**[デバイス]** ページの一覧で複数のデバイスを選択できます。 **[ジョブ]** パネルには、選択したすべてのデバイスに共通のメソッドの型が表示されます。

## <a name="reconfigure-a-device"></a>デバイスの再構成

デバイスの構成を変更するには、**[デバイス]** ページにあるデバイスの一覧でデバイスを選択し、**[ジョブ]** を選択し、**[再構成]** を選択します。 [ジョブ] パネルには、選択したデバイスについて変更可能なプロパティ値が表示されます。

![デバイスの再構成](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure.png)

変更を行うには、ジョブの名前を追加し、プロパティ値を更新して、**[適用]** を選択します。

![デバイスのプロパティ値の更新](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical.png)

**[メンテナンス]** ページでジョブの状態を追跡するには、**[ジョブ]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * シミュレートされたデバイスをプロビジョニングする。
> * シミュレートされたデバイスをテストする。
> * ソリューションからデバイス メソッドを呼び出す。
> * デバイスを再構成する。

デバイスの管理方法について説明したので、次に推奨される手順を以下に示します。

* [デバイスの問題をトラブルシューティングして修復する](iot-accelerators-remote-monitoring-maintain.md)。
* [シミュレートされたデバイスを使用してソリューションをテストする](iot-accelerators-remote-monitoring-test.md)。
* [デバイスをリモート監視ソリューション アクセラレータに接続する](iot-accelerators-connecting-devices-node.md)。

<!-- Next tutorials in the sequence -->