---
title: Azure Portal を使用して IoT Hub を作成する | Microsoft Docs
description: Azure Portal で Azure IoT Hub を作成、管理、および削除する方法。 価格レベル、スケーリング、セキュリティ、およびメッセージングの構成に関する情報が含まれています。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 6ffde076caff6217bf6255c9294eca63d3e39b85
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Azure Portal を使用して IoT Hub を作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

この記事では、次の内容について説明します。

* Azure Portal で IoT Hub サービスを検索する方法。
* IoT Hub を作成および管理する方法。

## <a name="where-to-find-the-iot-hub-service"></a>IoT Hub サービスを検索する場所

IoT Hub サービスは、ポータルの次の場所にあります。

* **[+ 新規]**、**[モノのインターネット]** の順に選択します。
* Marketplace で **[モノのインターネット]** を選択します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

IoT Hub の作成には、次の方法を使用できます。

* **[+ 新規]** オプションにより、次のスクリーン ショットに示されているブレードが開きます。 この方法および Marketplace を使用して IoT Hub を作成する手順は同じです。
* Marketplace で **[作成]** を選択して、次のスクリーン ショットに示されているブレードを開きます。

次のセクションでは、IoT Hub を作成する手順をいくつか説明します。

### <a name="choose-the-name-of-the-iot-hub"></a>IoT Hub の名前の選択

IoT Hub を作成するには、IoT ハブに名前を付ける必要があります。 この名前は、すべての IoT Hub で一意にする必要があります。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>価格レベルの選択

必要な機能およびソリューションで 1 日に送信するメッセージの数に応じて、複数のレベルから適切なものを選びます。 無料レベルは、テストおよび評価用です。 IoT Hub に接続できるデバイスは 500 個で、1 日に許可されるメッセージ数は最大 8,000 件です。 Azure サブスクリプションごとに、無料レベルの IoT Hub を 1 つ作成できます。 

他のレベルのオプションについて詳しくは、[適切な IoT Hub レベルの選択](iot-hub-scaling.md)に関するページをご覧ください。

### <a name="iot-hub-units"></a>IoT Hub ユニット

ユニットごとに許可される 1 日あたりのメッセージの数は、ハブの価格レベルによって決まります。 たとえば、IoT Hub で 700,000 件の受信メッセージをサポートする場合は、S1 レベルのユニットを 2 つ選択します。

### <a name="device-to-cloud-partitions-and-resource-group"></a>デバイスとクラウド間のパーティションおよびリソース グループ

IoT Hub のパーティションの数を変更することができます。 既定のパーティション数は 4 ですが、ドロップダウン リストから別の数を選択できます。

空のリソース グループを明示的に作成する必要はありません。 リソースを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。

![][5]

### <a name="choose-subscription"></a>サブスクリプションの選択

Azure IoT Hub では、ユーザー アカウントがリンクされている Azure サブスクリプションが自動的に表示されます。 IoT Hub を関連付ける Azure サブスクリプションを選択できます。

### <a name="choose-the-location"></a>場所の選択

場所のオプションでは、IoT Hub を使用できるリージョンのリストが示されます。

### <a name="create-the-iot-hub"></a>IoT Hub の作成

前の手順がすべて完了したら、IoT Hub を作成できます。 **[作成]** をクリックし、バックエンド プロセスを開始して、選択したオプションで IoT Hub を作成およびデプロイします。

適切な場所のサーバーでのバックエンド デプロイメントの実行には時間がかかるため、IoT Hub の作成には数分かかる場合があります。

## <a name="change-the-settings-of-the-iot-hub"></a>IoT Hub の設定変更

IoT Hub ブレードから IoT Hub を作成したら、既存の設定を変更できます。

![][8]

**共有アクセス ポリシー**: これらのポリシーは、IoT Hub に接続するデバイスとサービスのアクセス許可を定義します。 これらのポリシーには、**[一般]** にある **[共有アクセス ポリシー]** をクリックすることでアクセスできます。 このブレードでは、既存のポリシーを変更したり、新しいポリシーを追加したりすることができます。

### <a name="create-a-policy"></a>ポリシーの作成

* **[追加]** をクリックしてブレードを開きます。 このブレードで、次の図に示すように、新しいポリシーの名前と、このポリシーに関連付けるアクセス許可を入力できます。

    これらの共有ポリシーに関連付けることができるアクセス許可はいくつかあります。 **レジストリの読み取り**ポリシーと**レジストリの書き込み**ポリシーは、ID レジストリに対する読み取りと書き込みのアクセス権を付与します。 書き込みオプションを選択すると、読み取りオプションが自動的に選択されます。

    **サービス接続**ポリシーは、**デバイスからクラウドへの受信**など、サービス エンドポイントへのアクセス許可を付与します。 **デバイス接続**ポリシーは、IoT Hub デバイス側エンドポイントを使用してメッセージを送受信するためのアクセス許可を付与します。

* **[作成]** をクリックして、この新しく作成されたポリシーを既存のリストに追加します。

![][10]

## <a name="endpoints"></a>エンドポイント

**[エンドポイント]** をクリックして、変更対象となる IoT Hub のエンドポイントの一覧を表示します。 エンドポイントには、IoT Hub に組み込まれているエンドポイントと、IoT Hub を作成した後に追加したエンドポイントの 2 種類があります。

![][11]

### <a name="built-in-endpoints"></a>組み込みのエンドポイント

組み込みのエンドポイントには、**クラウドからデバイスへのフィードバック**と**イベント**の 2 つがあります。

* **クラウドからデバイスへのフィードバック**設定: この設定には、メッセージに関する **[クラウドからデバイスの TTL]** (有効期限) と **[保存期間]** (時間単位) の 2 つのサブ設定があります。 IoT Hub を最初に作成したときは、どちらも既定値の 1 時間に設定されます。 これらの設定を調整するには、スライダーを使用するか、値を入力します。
* **イベント**設定: この設定には複数のサブ設定があり、その中には読み取り専用のものもあります。 これらの設定を以下に示します。

  * **パーティション**: IoT Hub の作成時に既定値が設定されます。 この設定を使用してパーティションの数を変更できます。

  * **イベント ハブと互換性のある名前とエンドポイント**: IoT Hub の作成時に、イベント ハブが内部的に作成され、ユーザーは特定の状況ではアクセスが必要な場合があります。 イベント ハブと互換性のある名前とエンドポイントの値をカスタマイズすることはできませんが、**[コピー]** をクリックすることで値をコピーできます。

  * **保存期間**: 既定で 1 日に設定されますが、ドロップダウン リストを使用して値を変更できます。 デバイスからクラウド設定では、この値は日数です。

  * **コンシューマー グループ**: コンシューマー グループを使用すると、複数の閲覧者が、IoT Hub とは別にメッセージを読み取ることができます。 すべての IoT Hub は、既定のコンシューマー グループを使用して作成されます。 ただし、この設定を使用して、コンシューマー グループを IoT Hub に追加したり、削除したりすることができます。

  > [!NOTE]
  > 既定のコンシューマー グループを編集したり削除したりすることはできません。

### <a name="custom-endpoints"></a>カスタム エンドポイント

ポータルを使用して、IoT Hub にカスタム エンドポイントを追加できます。 **[エンドポイント]** ブレードで、上部にある **[追加]** をクリックして **[エンドポイントの追加]** ブレードを開きます。 必要な情報を入力し、**[OK]** をクリックします。 メインの **[エンドポイント]** ブレードに、カスタム エンドポイントが表示されます。

![][13]

カスタム エンドポイントの詳細については、「[リファレンス - IoT Hub エンドポイント][lnk-devguide-endpoints]」を参照してください。

## <a name="routes"></a>ルート

IoT Hub がデバイスからクラウドへのメッセージをディスパッチする方法を管理するには、**[ルート]** をクリックします。

![][14]

IoT Hub にルートを追加するには、**[ルート]*** ブレードの上部にある **[追加]** をクリックし、必要な情報を入力して、**[OK]** をクリックします。 メインの **[ルート]** ブレードにルートが表示されます。 ルートを編集するには、一覧でルートをクリックします。 ルートを有効にするには、一覧でルートをクリックし、**[有効/無効]** の切り替えを **[オフ]** に設定します。 変更を保存するには、ブレード下部の **[OK]** をクリックします。

![][15]

## <a name="delete-the-iot-hub"></a>IoT Hub の削除

削除する IoT Hub は、 **[参照]**をクリックしてから、削除対象の適切なハブを選択することで参照できます。 IoT Hub を削除するには、IoT Hub 名の下の **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理][lnk-bulk]
* [IoT Hub メトリック][lnk-metrics]
* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする][lnk-iotedge]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
