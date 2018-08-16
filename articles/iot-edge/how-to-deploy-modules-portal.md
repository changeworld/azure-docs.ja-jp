---
title: Azure IoT Edge モジュールのデプロイ (Portal) | Microsoft Docs
description: Azure Portal を使用して IoT Edge デバイスにモジュールをデプロイする
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 83f199c49209210ec577017534f93e36d05bd70a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620367"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure Portal から Azure IoT Edge モジュールをデプロイする

ビジネス ロジックで IoT Edge モジュールを作成したら、それらをデバイスにデプロイしてエッジで動作させます。 連携してデータを収集および処理する複数のモジュールがある場合は、一度にそのすべてをデプロイし、それらを接続するルーティング規則を宣言できます。 

この記事では、Azure Portal を使用して配置マニフェストを作成し、IoT Edge デバイスにデプロイをプッシュする方法を紹介します。 共有タグに基づいて複数のデバイスをターゲットとするデプロイの作成については、「[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。 
* IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device-portal.md)。 

## <a name="select-your-device"></a>デバイスを選択する

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
2. メニューから **[IoT Edge]** を選択します。
3. デバイスの一覧でターゲット デバイスの ID をクリックします。 
4. **[Set Modules] \(モジュールの設定)** を選択します。

## <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

Azure Portal には、JSON ドキュメントを手動で作成する代わりに配置マニフェストを作成する手順を示すウィザードがあります。 これは 3 つの手順 (**モジュールの追加**、**ルートの指定**、および**デプロイの確認**) で構成されます。 

### <a name="add-modules"></a>モジュールを追加する

1. ページの **[レジストリ設定]** セクションで、モジュール イメージを格納するプライベート コンテナー レジストリにアクセスするための資格情報を指定します。 
2. ページの **[Deployment modules]\(デプロイ モジュール\)** セクションで、**[追加]** を選択します。 
3. ドロップダウン リストでモジュールの種類を確認します。 
   * **IoT Edge モジュール** - 既定のオプションです。
   * **Azure Stream Analytics モジュール** - Azure Stream Analytics ワークロードから生成されるモジュールのみです。 
4. **[IoT Edge モジュール]** を選択します。
5. モジュールの名前を入力し、コンテナー イメージを指定します。 例:  
   * **名前** - tempSensor
   * **イメージの URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. 必要な場合は、省略可能なフィールドに入力します。 コンテナー作成オプション、再起動ポリシー、および必要な状態について詳しくは、「[edgeAgent の必要なプロパティ](module-edgeagent-edgehub.md#edgeagent-desired-properties)」をご覧ください。 モジュール ツインについて詳しくは、「[必要なプロパティの定義または更新](module-composition.md#define-or-update-desired-properties)」をご覧ください。
7. **[保存]** を選択します。
8. 手順 2 ～ 6 を繰り返してモジュールをデプロイに追加します。 
9. **[次へ]** を選択して、ルートのセクションに進みます。

### <a name="specify-routes"></a>ルートを指定する

既定では、**FROM /\* INTO $upstream** として定義済みの **route** という名前のルートがウィザードから提供されます。つまり、モジュールによるメッセージ出力は IoT ハブに送信されます。  

[ルートの宣言](module-composition.md#declare-routes)の情報を使用してルートを追加または更新し、**[次へ]** を選択して確認のセクションに進みます。

### <a name="review-deployment"></a>デプロイを確認する

確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 追加しなかった 2 つのモジュール (**$edgeAgent** および **$edgeHub**) が宣言されていることに注目してください。 これらの 2 つのモジュールは、[IoT Edge ランタイム](iot-edge-runtime.md)を構成し、すべてのデプロイの既定値として使用されます。 

デプロイ情報を確認し、**[送信]** を選択します。 

## <a name="view-modules-on-your-device"></a>デバイス上のモジュールを表示する

モジュールをデバイスにデプロイした後で、そのすべてを Portal の **[デバイスの詳細]** ページで表示できます。 このページには、デプロイした各モジュールの名前、およびデプロイ状態や終了コードなどの役立つ情報が表示されます。 

## <a name="next-steps"></a>次の手順

[大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)の方法を学習します
