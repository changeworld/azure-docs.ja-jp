---
title: "構成済みソリューションのカスタマイズ | Microsoft Docs"
description: "Azure IoT Suite の構成済みソリューションのカスタマイズ方法に関するガイダンスを提供します。"
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 14e2fcea9a6afbac640d665d5e44a700f855db4b
ms.openlocfilehash: bbec0c01e8760c975222768e694e57b8b447bb3b


---
# <a name="customize-a-preconfigured-solution"></a>構成済みソリューションのカスタマイズ
Azure IoT Suite で提供される構成済みソリューションを利用すれば、スイート内のサービスを連動させ、エンド ツー エンド ソリューションを提供できます。 これを出発点として、いくつかのポイントで特定のシナリオに合わせてソリューションを拡張したり、カスタマイズしたりできます。 次のセクションでは、これらの一般的なカスタマイズ ポイントの概要を示します。

## <a name="finding-the-source-code"></a>ソース コードの検索
構成済みソリューションのソース コードは、次のリポジトリの Github で入手できます。

* リモート監視: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* 予測的なメンテナンス: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

構成済みソリューションのソース コードは、Azure IoT Suite を使用して IoT ソリューションのエンド ツー エンド機能を実装する際に使用されるパターンとプラクティスを示すために提供されています。 ソリューションをビルドしてデプロイする方法の詳細については、GitHub リポジトリを参照してください。

## <a name="changing-the-preconfigured-rules"></a>構成済みルールの変更
リモート監視ソリューションには、ソリューション内でデバイス情報、テレメトリ、およびルールのロジックを処理するための&3; つの [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ジョブが含まれています。

この&3; つの Stream Analytics ジョブとその構文については、「[リモート監視の事前構成済みソリューションのチュートリアル](iot-suite-remote-monitoring-sample-walkthrough.md)」で詳しく説明しています。 

これらのジョブを直接編集し、ロジックを変更したり、シナリオに固有のロジックを追加したりすることができます。 Stream Analytics ジョブを見つけるには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)に移動します。
2. IoT ソリューションと同じ名前のリソース グループに移動します。 
3. 変更を加える Azure Stream Analytics ジョブを選択します。 
4. コマンド セットから **[停止]** を選択してジョブを停止します。 
5. 入力、クエリ、および出力を編集します。
   
    変更を簡単に加えるには、**Rules** ジョブのクエリを、**">"** の代わりに **"<"** を使用するように変更します。 ルールを編集してもソリューション ポータルには引き続き **">"** が表示されますが、基になるジョブを変更したため動作は反転されます。
6. ジョブの開始

> [!NOTE]
> リモート監視ダッシュボードは特定のデータによって異なるため、ジョブを変更するとダッシュボードで障害が発生する可能性があります。
> 
> 

## <a name="adding-your-own-rules"></a>独自のルールの追加
構成済みの Azure Stream Analytics ジョブの変更だけでなく、Azure ポータルを使用して、新しいジョブを追加したり、新しいクエリを既存のジョブに追加したりできます。

## <a name="customizing-devices"></a>デバイスのカスタマイズ
最も一般的な拡張アクティビティの&1; つは、シナリオに固有のデバイスの操作です。 デバイスを操作するためのいくつかの方法があります。 これらの方法には、シナリオに合わせたシミュレーション対象デバイスの変更や、[IoT デバイス SDK][IoT Device SDK] を使用したソリューションへの物理デバイスの接続が含まれます。

デバイス追加の詳細な手順については、[Iot Suite 接続デバイス](iot-suite-connecting-devices.md)に関するページと、リモート監視の構成済みソリューションと連携するように設計された[リモート監視 C SDK サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring)を参照してください。

### <a name="creating-your-own-simulated-device"></a>独自のシミュレーション対象デバイスの作成
[リモート監視ソリューションのソース コード](https://github.com/Azure/azure-iot-remote-monitoring) には .NET シミュレーターが含まれています。 このシミュレーターはソリューションの一部としてプロビジョニングされたものであり、異なるメタデータやテレメトリを送信したり、別のコマンドに応答したりするように変更できます。

リモート監視の事前構成済みソリューションに含まれるこの事前構成済みシミュレーターでは、温度と湿度に関するテレメトリを出力する冷却デバイスをシミュレートしています。 GitHub リポジトリをフォークしている場合、[Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) プロジェクトに含まれるシミュレーターを変更できます。

### <a name="available-locations-for-simulated-devices"></a>シミュレーション対象デバイスで利用可能な場所
既定の場所のセットでは、米国ワシントン州のシアトル/レドモンドとなっています。 これらの場所は [SampleDeviceFactory.cs][lnk-sample-device-factory] で変更できます。

### <a name="building-and-using-your-own-physical-device"></a>独自の (物理) デバイスの構築と使用
[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) では、IoT ソリューションにさまざまな種類のデバイス (言語およびオペレーティング システム) を接続するためのライブラリが提供されます。

## <a name="modifying-dashboard-limits"></a>ダッシュボードの制限の変更
### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>ダッシュボードのドロップダウン リストに表示されるデバイスの数
既定では 200 です。 この数は [DashboardController.cs][lnk-dashboard-controller] で変更できます。

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Bing 地図コントロールに表示されるピンの数
既定では 200 です。 この数は [TelemetryApiController.cs][lnk-telemetry-api-controller-01] で変更できます。

### <a name="time-period-of-telemetry-graph"></a>テレメトリ グラフの期間
既定では 10 分です。 この値は、[TelemetryApiController.cs][lnk-telemetry-api-controller-02] で変更できます。

## <a name="manually-setting-up-application-roles"></a>アプリケーション ロールの手動設定
以下の手順では、**Admin** および **ReadOnly** アプリケーション ロールを構成済みソリューションに追加する方法を説明しています。 azureiotsuite.com サイトからプロビジョニングされた構成済みのソリューションには既に **Admin** ロールと **ReadOnly** ロールが含まれています。

**ReadOnly** ロールのメンバーはダッシュボードとデバイス一覧を表示できますが、デバイスの追加、デバイス属性の変更、またはコマンドの送信は実行できません。  **Admin** ロールのメンバーは、ソリューション内のすべての機能へのフル アクセス権を持ちます。

1. [Azure クラシック ポータル][lnk-classic-portal]に移動します。
2. **[Active Directory]**を選択します。
3. ソリューションをプロビジョニングするときに使用した AAD テナントの名前をクリックします。
4. **[アプリケーション]**をクリックします。
5. 構成済みソリューション名と一致するアプリケーションの名前をクリックします。 一覧にアプリケーションが表示されない場合は、**[表示]** ボックスの一覧で **[自分の会社が所有するアプリケーション]** を選択し、チェック マークをクリックします。
6. ページ下部の **[マニフェストの管理]**、**[マニフェストのダウンロード]** の順にクリックします。
7. これにより、.json ファイルがローカル コンピューターにダウンロードされます。  このファイルを好みのテキスト エディターで開いて編集します。
8. .json ファイルの&3; 行目には次のように記載されています。
   
   ```
   "appRoles" : [],
   ```
   この行を次のコードに置き換えます。
   
   ```
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```
9. 更新された .json ファイルを保存します (既存のファイルを上書きできます)。
10. Azure クラシック ポータルのページ下部で **[マニフェストの管理]**、**[マニフェストのアップロード]** の順に選択し、前の手順で保存した .json ファイルをアップロードします。
11. これで、**Admin** ロールと **ReadOnly** ロールがアプリケーションに追加されました。
12. これらのロールのいずれかをディレクトリ内のユーザーに割り当てるには、「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」をご覧ください。

## <a name="feedback"></a>フィードバック
このドキュメントでの説明をご希望のカスタマイズがある場合は、 [ユーザーの意見募集](https://feedback.azure.com/forums/321918-azure-iot)のページで機能の提案を投稿するか、この記事の下部でコメントしてください。 

## <a name="next-steps"></a>次のステップ
構成済みのソリューションをカスタマイズするためのオプションの詳細については、次のリンク先をご覧ください。

* [ロジック アプリを Azure IoT Suite リモート監視構成済みソリューションに接続する][lnk-logicapp]
* [事前構成済みのリモート監視ソリューションによる動的テレメトリの使用][lnk-dynamic]
* [リモート監視構成済みソリューションのデバイス情報メタデータ][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com



<!--HONumber=Feb17_HO2-->


