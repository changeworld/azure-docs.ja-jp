---
title: Azure Data Box Edge デバイスを返却する | Microsoft Docs
description: Azure Data Box Edge デバイスを返却して、デバイスに対する注文を削除する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468460"
---
# <a name="return-your-azure-data-box-edge-device"></a>Azure Data Box Edge を返却する

この記事では、データをワイプして、ご利用の Azure Data Box Edge デバイスを返却する方法について説明します。 デバイスを返却した後、デバイスと関連付けられたリソースを削除することもできます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * デバイス上のデータ ディスクのデータを削除する
> * サポート チケットを開いてデバイスを返却する
> * デバイスを選択して集荷をスケジュールする
> * Azure portal でリソースを削除する

## <a name="erase-data-from-the-device"></a>デバイスからデータを消去する

デバイス上のデータ ディスクのデータを削除するには、ご利用のデバイスをリセットする必要があります。 ローカル Web UI または PowerShell インターフェイスを使用して、デバイスをリセットできます。

リセットする前に、必要に応じて、デバイス上のローカル コピーを作成します。 デバイスから Azure Storage コンテナーにデータをコピーできます。

ローカル Web UI を使用してデバイスをリセットするには、次の手順を行います。

1. ローカル Web UI で、**[メンテナンス] > [デバイスのリセット]** に移動します。
2. **[デバイスのリセット]** を選択します。

    ![デバイスをリセットする](media/data-box-edge-return-device/device-reset-1.png)

3. 確認を求めるメッセージが表示されたら、警告を確認し、**[はい]** を選択して続行します。

    ![リセットの確認](media/data-box-edge-return-device/device-reset-2.png)  

このリセットによって、デバイスのデータ ディスクのデータが消去されます。 ご利用のデバイス上のデータ量に応じて、このプロセスには約 30 から 40 分かかります。

または、デバイスの PowerShell インターフェイスに接続して、`Reset-HcsAppliance` コマンドレットを使用し、データ ディスクからデータを消去します。 詳細については、「[デバイスをリセットする](data-box-edge-connect-powershell-interface.md#reset-your-device)」を参照してください。

> [!NOTE]
> - 新しいデバイスに交換またはアップグレードする場合は、新しいデバイスを受け取ってからご利用のデバイスをリセットすることをお勧めします。
> - デバイスのリセットでは、デバイスからすべてのローカル データが削除されるだけです。 クラウド内にあるデータは削除されることなく、[料金](https://azure.microsoft.com/pricing/details/storage/)がかかります。 このデータは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのクラウド ストレージ管理ツールを使用して、個別に削除する必要があります。

## <a name="open-a-support-ticket"></a>サポート チケットを開く

返却プロセスを開始するには、次の手順を行います。

1. デバイスを返却したいことを示して、Microsoft サポートでサポート チケットを開きます。 問題の種類に **[Data Box Edge Hardware]\(Data Box Edge ハードウェア\)** を選択します。

    ![サポート チケットを開く](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Microsoft サポート エンジニアからご連絡いたします。 発送先の詳細を指定します。
3. 返送用ボックスが必要な場合は、要求することができます。 **[Need an empty box to return]\(返却するために空き箱が必要\)** の質問に **[はい]** と回答します。


## <a name="schedule-a-pickup"></a>集荷をスケジュールする

1. デバイスをシャットダウンします。 ローカル Web UI で、**[メンテナンス] > [Power settings]\(電源設定\)** に移動します。
2. **[シャットダウン]** を選択します。 確認を求めるメッセージが表示されたら、**[はい]** をクリックして続行します。 詳細については、「[電源を管理する](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)」を参照してください。
3. 電源ケーブルを外して、デバイスからネットワーク ケーブルをすべて取り外します。
4. 自分の箱または Azure から受け取った空き箱を使用して、出荷パッケージを準備します。 デバイスとそのデバイスに同梱されていた電源コードを箱に入れます。
5. Azure から受け取った出荷ラベルをパッケージに貼り付けます。
6. リージョンの運送業者で集荷をスケジュールします。 米国でデバイスを返却する場合、運送業者は UPS です。 集荷のスケジュールを設定するには:

    1. 最寄りの UPS (国固有のフリー ダイヤル) に連絡します。
    2. 電話で、印刷ラベルに表示されている返送追跡番号を伝えます。
    3. 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box Edge を持ち込むこともできます。

## <a name="delete-the-resource"></a>リソースを削除する

デバイスが Azure データセンターに到着した後、デバイスは破損や改ざんの兆候がないかを検証されます。

- デバイスがそのまま良好な状態で到着した場合、課金メーターはそのリソースに対して停止されます。 Microsoft サポートが、デバイスが返却されたことを確認するためにご連絡します。 その後、Azure portal でデバイスと関連付けられたリソースを削除することができます。
- デバイスが大幅に破損して到着した場合、罰金が適用される可能性があります。 詳細については、[紛失または破損しているデバイスに関する FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) に関するページと[製品のサービス使用条件](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。  


次の場合に Azure portal でデバイスを削除できます。
-   注文した後とデバイスが Microsoft によって準備される前。
-   デバイスを Microsoft に返却し、Azure データセンターでの物理検査に合格して、Microsoft サポートがデバイスが返却されたことを確認する連絡をした後。

別のサブスクリプションまたは場所に対してデバイスを有効にしている場合、Microsoft によって 1 営業日以内に新しいサブスクリプションまたは場所に注文が移動されます。 注文が移動された後、このリソースを削除できます。


Azure portal でデバイスとリソースを削除するには、次の手順を行います。

1. Azure portal で自分のリソースに移動し、**[概要]** に移動します。 コマンド バーで、**[削除]** を選択します。

    ![[削除] の選択](media/data-box-edge-return-device/delete-resource-1.png)

2. **[デバイスの削除]** ブレードで、削除するデバイスの名前を入力して **[削除]** を選択します。

    ![削除の確定](media/data-box-edge-return-device/delete-resource-2.png)

デバイスと関連付けられたリソースが正常に削除されたら、通知されます。

## <a name="next-steps"></a>次の手順

- [帯域幅を管理する](data-box-edge-manage-bandwidth-schedules.md)方法について学習します。
