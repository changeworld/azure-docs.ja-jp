---
title: Azure ベースのリモート監視ソリューションでのデバイスの管理 | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータに接続されているデバイスを管理する方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/12/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63baf6397b2542311525bac740c50b5eacbd35cf
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097429"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>チュートリアル: 監視ソリューションに接続されているデバイスの構成および管理

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されている IoT デバイスを構成および管理します。 ソリューション アクセラレータに新しいデバイスを追加し、デバイスを構成し、デバイスのファームウェアを更新します。

Contoso は自社の施設の 1 つを拡大するために、新しい機械を注文しました。 新しい機械が納品されるのを待つ間に、あなたはソリューションの動作をテストするためのシミュレーションを実施したいと考えています。 シミュレーションを実行するために、新しいシミュレートされたエンジン デバイスをリモート監視ソリューション アクセラレータに追加し、このシミュレートされたデバイスがアクションと構成の更新に正しく応答することをテストします。

デバイスを構成および管理する拡張可能な方法を提供するために、リモート監視ソリューション アクセラレータでは[ジョブ](../iot-hub/iot-hub-devguide-jobs.md)や[ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md)などの IoT Hub 機能を使用します。 このチュートリアルではシミュレートされたデバイスを使用していますが、デバイス開発者は、[リモート監視ソリューション アクセラレータに接続された物理デバイス](iot-accelerators-connecting-devices.md)にダイレクト メソッドを実装できます。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * シミュレートされたデバイスをプロビジョニングする。
> * シミュレートされたデバイスをテストする。
> * デバイスのファームウェアを更新する。
> * デバイスを再構成する。
> * デバイスを整理する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューション アクセラレータのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューション アクセラレータをデプロイしていない場合は、クイック スタート「[クラウドベースのリモート監視ソリューションのデプロイ](quickstart-remote-monitoring-deploy.md)」を完了する必要があります。

## <a name="add-a-simulated-device"></a>シミュレートされたデバイスの追加

ソリューションの **[デバイス]** ページに移動して、**[+ 新規デバイス]** をクリックします。

[![シミュレートされたデバイスのプロビジョニング](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

**[新規デバイス]** パネルで、**[Simulated]\(シミュレート済み\)** を選択し、プロビジョニングするデバイスの数を **1** のままにします。**[Faulty Engine]\(問題があるエンジン\)** デバイス モデルを選択し、**[適用]** を選択して、シミュレートされたデバイスを作成します。

[![シミュレートされたエンジン デバイスのプロビジョニング](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>シミュレートされたデバイスのテスト

シミュレートされたデバイスがテレメトリとレポートのプロパティ値を送信していることをテストするには、**[デバイス]** ページのデバイスの一覧でデバイスを選択します。 デバイスに関するライブ情報が **[デバイスの詳細]** パネルに表示されます。

[![新しくシミュレートされたエンジン デバイスの表示](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

**[デバイスの詳細]** で、新しいデバイスがテレメトリを送信していることを確認します。 デバイスからのさまざまな振動テレメトリ ストリームを表示するには、**[振動]** をクリックします。

[![表示するテレメトリ ストリームの選択](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

**[デバイスの詳細]** パネルには、デバイスに関するその他の情報 (タグ値、デバイスがサポートするメソッド、デバイスから報告されるプロパティなど) が表示されます。

詳細な診断を確認するには、下方にスクロールして **[診断]** を表示します。

[![デバイスの診断を表示する](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-expanded.png#lightbox)

## <a name="act-on-a-device"></a>デバイスでの動作

ソリューション アクセラレータから開始されたアクションにシミュレートされたエンジン デバイスが正しく応答することをテストするには、**FirmwareUpdate** メソッドを実行します。 メソッドを実行してデバイスを操作するには、デバイスの一覧でデバイスを選択し、**[ジョブ]** をクリックします。 複数のデバイスを操作する場合は、複数のデバイスを選択できます。 **[ジョブ]** パネルで、**[Run method]\(実行メソッド\)** を選択します。 **エンジン** デバイス モデルでは、**FirmwareUpdate**、**FillTank**、および **EmptyTank** の 3 つのメソッドを指定します。

[![エンジンのメソッド](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

**[FirmwareUpdate]** を選択します。ジョブ名を **UpdateEngineFirmware**、ファームウェア バージョンを **2.0.0**、ファームウェア URI を **http://contoso.com/engine.bin** に設定し、**[適用]** をクリックします。

[![ファームウェア更新メソッドをスケジュールする](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

ジョブの状態を追跡するには、**[View job status]\(ジョブの状態の表示\)** をクリックします。

[![スケジュールされたファームウェア更新ジョブを監視する](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

ジョブが完了したら、**[デバイス]** ページに戻ります。 新しいファームウェア バージョンがエンジン デバイスに対して表示されます。

**[デバイス]** ページで種類が異なる複数のデバイスを選択した場合でも、それらの複数のデバイス上でメソッドを実行するジョブを作成できます。 **[ジョブ]** パネルには、選択したすべてのデバイスに共通のメソッドのみが表示されます。

## <a name="reconfigure-a-device"></a>デバイスの再構成

エンジンの構成プロパティを更新できることをテストするには、**[デバイス]** ページのデバイスの一覧でデバイスを選択します。 次に、**[ジョブ]** をクリックし、**[再構成]** を選択します。 [ジョブ] パネルには、選択したデバイスの更新可能なプロパティ値が表示されます。

[![デバイスの再構成](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

エンジンの場所を更新するために、ジョブ名を **UpdateEngineLocation**、経度を **-122.15**、場所を **Factory 2**、緯度を **47.62** に設定し、**[適用]** をクリックします。

[![デバイスのプロパティ値の更新](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

ジョブの状態を追跡するには、**[View job status]\(ジョブの状態の表示\)** をクリックします。

[![デバイスのプロパティ値の更新](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

ジョブが完了したら、**[ダッシュボード]** ページに戻ります。 エンジン デバイスがマップ上の新しい場所に表示されます。

[![エンジンの場所を表示する](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>デバイスを整理する

オペレーターがより簡単にデバイスを整理および管理できるように、それぞれのチーム名でデバイスにタグを付けます。 Contoso には、フィールド サービス アクティビティのための 2 つの異なるチームがあります。

* Smart Vehicle (スマート車両) チームは、トラックとプロトタイプ作成デバイスを管理します。
* Smart Building (スマート ビルディング) チームは、冷却装置、エレベーター、エンジンを管理します。

すべてのデバイスを表示するために、**[デバイス]** ページに移動して **[すべてのデバイス]** フィルターを選択します。

[![すべてのデバイスを表示する](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>タグを追加する

すべての**トラック** デバイスと**プロトタイプ作成**デバイスを選択します。 次に、**[ジョブ]** をクリックします。

[![プロトタイプ作成デバイスとトラック デバイスを選択する](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-expanded.png#lightbox)

**[タグ]** を選択し、ジョブ名を **AddConnectedVehicleTag** に設定します。**FieldService** という名前のテキスト タグを追加し、値 **ConnectedVehicle** を設定します。 次に、**[適用]** をクリックします。

[![プロトタイプ作成デバイスとトラック デバイスにタグを追加する](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

[デバイス] ページで、すべての**冷却装置**、**エレベーター**、および**エンジン** デバイスを選択します。 次に、**[ジョブ]** をクリックします。

[![冷却装置、エレベーター、およびエンジン デバイスを選択する](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-expanded.png#lightbox)

**[タグ]** を選択し、ジョブ名を **AddSmartBuildingTag** に設定します。**FieldService** という名前のテキスト タグを追加し、値 **SmartBuilding** を設定します。 次に、**[適用]** をクリックします。

[![冷却装置、エレベーター、およびエンジン デバイスにタグを追加する](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>フィルターの作成

タグ値を使用してフィルターを作成できます。 **[デバイス]** ページで、**[Manage device groups]\(デバイス グループの管理\)** をクリックします。

[![デバイス グループの管理](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

条件にタグ名 **FieldService** と値 **SmartBuilding** を使用してテキスト フィルターを作成します。 フィルターに **[Smart Building]\(スマート ビルディング\)** という名前を付けて保存します。

[![[Smart Building]\(スマート ビルディング\) フィルターを作成する](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

条件にタグ名 **FieldService** と値 **ConnectedVehicle** を使用してテキスト フィルターを作成します。 フィルターに **[Connected Vehicle]\(接続された車両\)** という名前を付けて保存します。

[![[Connected Vehicle]\(接続された車両\) フィルターを作成する](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

これで、Contoso のオペレーターは、運用チームに基づいてデバイスを照会することができます。

[![[Connected Vehicle]\(接続された車両\) フィルターを作成する](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルに進む場合は、リモート監視ソリューション アクセラレータをデプロイしたままにしておきます。 ソリューション アクセラレータを使用していないときの実行コストを削減するには、設定パネルでシミュレートされたデバイスを停止します。

[![テレメトリを一時停止する](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-expanded.png#lightbox)

次のチュートリアルを開始する準備ができたら、シミュレートされたデバイスを再起動することができます。

ソリューション アクセラレータが不要になった場合は、[[プロビジョニングされたソリューション]](https://www.azureiotsolutions.com/Accelerators#dashboard) ページから削除します。

![ソリューションを削除する](media/iot-accelerators-remote-monitoring-manage/deletesolution.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューション アクセラレータに接続されているデバイスを構成および管理する方法を示しました。 ソリューション アクセラレータを使用して接続されているデバイスの問題を特定して修正する方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [デバイス アラートを使用して、監視ソリューションに接続されているデバイスの問題を特定し、解決する](iot-accelerators-remote-monitoring-maintain.md)
