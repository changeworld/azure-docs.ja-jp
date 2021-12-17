---
title: Azure Data Box Heavy の注文方法のチュートリアル | Microsoft Docs
description: このチュートリアルでは、オンプレミスのデータを Azure にインポートできるようにするハイブリッド ソリューションである Azure Data Box Heavy とその注文方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: alkohli
ms.localizationpriority: high
ms.custom: contperf-fy22q1
ms.openlocfilehash: 0bf9eb2eab829e22e72f19fc4da4575166ea2755
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743348"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>チュートリアル:Azure Data Box Heavy を注文する


Azure Data Box Heavy は、迅速かつ簡単な信頼性の高い方法でオンプレミス データを Azure にインポートできるハイブリッド ソリューションです。 お客様は、Microsoft 提供の 770 TB (おおよその使用可能容量) ストレージ デバイスにデータを転送した後、そのデバイスを Microsoft に戻します。 その後、このデータは Azure にアップロードされます。

このチュートリアルでは、Azure Data Box Heavy を注文する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Data Box Heavy の前提条件
> * Data Box Heavy を注文する
> * 注文を追跡する
> * 注文をキャンセルする

## <a name="prerequisites"></a>前提条件

デバイスを展開する前に、Data Box サービスとデバイスに関する次の構成の前提条件を完了してください。

### <a name="for-installation-site"></a>設置場所では

開始する前に次の点を確認します。

- デバイスが標準的な出入り口や通路を通過できること。 デバイスが搬入経路全体を問題なく通過できることを確認してください。 デバイスのサイズは次のとおりです。幅:26 インチ、長さ: 48 インチ、高さ: 28 インチ。
- 1 階以外のフロアに設置する場合、エレベーターまたはスロープからデバイスを搬入できること。 デバイスの重量は約 500 ポンドです。
- データセンター内に、これだけの設置面積を占めるデバイスを収容可能な平らな場所があり、その近くに使用可能なネットワーク接続用設備があること。

### <a name="for-service"></a>サービスでは

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。
- デバイスが開梱されていること。
- データセンター ネットワークに接続されているホスト コンピューターがあること。 Data Box Heavy はこのコンピューターからデータをコピーします。 ホスト コンピューターでは、[Azure Data Box Heavy のシステム要件](data-box-system-requirements.md)に関する記事に記載されているサポート対象オペレーティング システムが実行されている必要があります。
- RJ-45 ケーブルを使用してローカル UI に接続し、デバイスを構成するためのノート PC があること。 このノート PC を使用して、デバイスの各ノードで 1 回構成を行います。
- お客様のデータセンターには、高速ネットワークが必要です。 少なくとも 1 本の 10 GbE 接続を利用することを強くお勧めします。
- デバイス ノードごとに 40 Gbps または 10 Gbps のケーブルが 1 本あること。 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) ネットワーク インターフェイスと互換性のあるケーブルを選択します。

    - 40 Gbps ケーブルの場合、ケーブルのデバイス側の端は QSFP+ である必要があります。
    - 10 Gbps ケーブルの場合、SFP+ ケーブルが必要です。ケーブルの一端を 10 G スイッチに差し込み、QSFP+ から SFP+ へのアダプター (QSA アダプター) を使用して他端をデバイスに差し込みます。
    - 電源ケーブルは、デバイスに付属しています。

## <a name="order-data-box-heavy"></a>Data Box Heavy を注文する

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

お客様の要件について詳しい情報を把握し、Data Box Heavy サービスが目的の場所で利用できるかどうかを確認するために、Data Box チームから連絡を差し上げます。 注文はその確認中、 **[Ordered]\(注文済み\)** 状態のままとなります。 何かの理由で注文を履行できない場合はお客様に通知が届きます。

## <a name="track-the-order"></a>注文を追跡する

注文後、Azure portal から注文の状態を追跡できます。 Data Box Heavy の注文に移動し、 **[概要]** に移動して状態を表示します。 ポータルでは、注文は、 **[注文済み]** 状態で表示されます。

デバイスが使用できない場合は、通知されます。 デバイスが使用可能な場合は、Microsoft が配送用のデバイスを特定し、配送の準備をします。 デバイスの準備中に、次のアクションが実行されます。

- デバイスに関連付けられているストレージ アカウントごとに SMB 共有が作成されます。
- 各共有で、ユーザー名やパスワードなどのアクセス資格情報が生成されます。
- デバイスのロック解除に役立つデバイス パスワードも生成されます。
- Data Box Heavy は、デバイスへの不正アクセスを防ぐためにどの時点でもロックされます。

デバイスの準備が完了すると、ポータルでは、注文が **[処理済み]** 状態で表示されます。

![処理された Data Box Heavy の注文](media/data-box-overview/data-box-order-status-processed.png)

次に、Microsoft は、デバイスを準備して地域の運送業者を通じて発送します。 デバイスが出荷されると、お客様に追跡番号が送信されます。 ポータルには、**出荷済み** 状態の注文が表示されます。

![発送された Data Box Heavy の注文](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>注文をキャンセルする

この注文をキャンセルするには、Azure portal で **[概要]** に移動し、コマンド バーの **[キャンセル]** をクリックします。

注文は、注文した後、注文の状態が [処理済み] とマークされるまでの間の任意の時点でキャンセルできます。
 
キャンセルされた注文を削除するには、 **[概要]** に移動し、コマンド バーの **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box Heavy に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 前提条件
> * Data Box Heavy を注文する
> * 注文を追跡する
> * 注文をキャンセルする

次のチュートリアルに進み、Data Box Heavy を設定する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box Heavy を設定する](./data-box-heavy-deploy-set-up.md)
