---
title: Azure Stack Edge デバイスを返却または交換する | Microsoft Docs
description: Azure Stack Edge デバイスを返却または交換する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743632"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスを返却または交換する

この記事では、データをワイプして、ご利用の Azure Stack Edge デバイスを返却する方法について説明します。 デバイスを返却した後、デバイスに関連付けられたリソースを削除、または交換用デバイスを注文することもできます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * デバイス上のデータ ディスクのデータを削除する
> * サポート チケットを開いてデバイスを返却する
> * デバイスを選択して集荷をスケジュールする
> * Azure portal でリソースを削除する
> * 交換用デバイスを入手する

## <a name="erase-data-from-the-device"></a>デバイスからデータを消去する

デバイス上のデータ ディスクのデータを削除するには、ご利用のデバイスをリセットする必要があります。 ローカル Web UI または PowerShell インターフェイスを使用して、デバイスをリセットできます。

リセットする前に、必要に応じて、デバイス上のローカル コピーを作成します。 デバイスから Azure Storage コンテナーにデータをコピーできます。

ローカル Web UI を使用してデバイスをリセットするには、次の手順を行います。

1. ローカル Web UI で、 **[メンテナンス] > [デバイスのリセット]** に移動します。
2. **[デバイスのリセット]** を選択します。

    ![デバイスをリセットする](media/azure-stack-edge-return-device/device-reset-1.png)

3. 確認を求めるメッセージが表示されたら、警告を確認し、 **[はい]** を選択して続行します。

    ![リセットの確認](media/azure-stack-edge-return-device/device-reset-2.png)  

このリセットによって、デバイスのデータ ディスクのデータが消去されます。 ご利用のデバイス上のデータ量に応じて、このプロセスには約 30 から 40 分かかります。

または、デバイスの PowerShell インターフェイスに接続して、`Reset-HcsAppliance` コマンドレットを使用し、データ ディスクからデータを消去します。 詳細については、「[デバイスをリセットする](azure-stack-edge-connect-powershell-interface.md#reset-your-device)」を参照してください。

> [!NOTE]
> - 新しいデバイスに交換またはアップグレードする場合は、新しいデバイスを受け取ってからご利用のデバイスをリセットすることをお勧めします。
> - デバイスのリセットでは、デバイスからすべてのローカル データが削除されるだけです。 クラウド内にあるデータは削除されることなく、[料金](https://azure.microsoft.com/pricing/details/storage/)がかかります。 このデータは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのクラウド ストレージ管理ツールを使用して、個別に削除する必要があります。

## <a name="open-a-support-ticket"></a>サポート チケットを開く

返却プロセスを開始するには、次の手順を行います。

1. デバイスを返却したいことを示して、Microsoft サポートでサポート チケットを開きます。 問題の種類に **[Azure Stack Edge Hardware]\(Azure Stack Edge ハードウェア\)** を選択します。

    ![サポート チケットを開く](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Microsoft サポート エンジニアからご連絡いたします。 発送先の詳細を指定します。
3. 返送用ボックスが必要な場合は、要求することができます。 **[Need an empty box to return]\(返却するために空き箱が必要\)** の質問に **[はい]** と回答します。


## <a name="schedule-a-pickup"></a>集荷をスケジュールする

1. デバイスをシャットダウンします。 ローカル Web UI で、 **[メンテナンス] > [Power settings]\(電源設定\)** に移動します。
2. **[シャットダウン]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして続行します。 詳細については、「[電源を管理する](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)」を参照してください。
3. 電源ケーブルを外して、デバイスからネットワーク ケーブルをすべて取り外します。
4. 自分の箱または Azure から受け取った空き箱を使用して、出荷パッケージを準備します。 デバイスとそのデバイスに同梱されていた電源コードを箱に入れます。
5. Azure から受け取った出荷ラベルをパッケージに貼り付けます。
6. リージョンの運送業者で集荷をスケジュールします。 米国でデバイスを返却する場合、UPS または FedEx の運送業者をご利用になれます。 UPS に集荷をスケジュールするには:

    1. 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    2. 電話で、印刷ラベルに表示されている返送追跡番号を伝えます。
    3. 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Azure Stack Edge を持ち込むこともできます。

## <a name="delete-the-resource"></a>リソースを削除する

デバイスが Azure データセンターに到着した後、デバイスは破損や改ざんの兆候がないかを検証されます。

- デバイスがそのまま良好な状態で到着した場合、課金メーターはそのリソースに対して停止されます。 Microsoft サポートが、デバイスが返却されたことを確認するためにご連絡します。 その後、Azure portal でデバイスと関連付けられたリソースを削除することができます。
- デバイスが大幅に破損して到着した場合、罰金が適用される可能性があります。 詳細については、[紛失または破損しているデバイスに関する FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) に関するページと[製品のサービス使用条件](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。  


次の場合に Azure portal でデバイスを削除できます。
-    注文した後とデバイスが Microsoft によって準備される前。
-    デバイスを Microsoft に返却し、Azure データセンターでの物理検査に合格して、Microsoft サポートがデバイスが返却されたことを確認する連絡をした後。

別のサブスクリプションまたは場所に対してデバイスを有効にしている場合、Microsoft によって 1 営業日以内に新しいサブスクリプションまたは場所に注文が移動されます。 注文が移動された後、このリソースを削除できます。


Azure portal でデバイスとリソースを削除するには、次の手順を行います。

1. Azure portal で自分のリソースに移動し、 **[概要]** に移動します。 コマンド バーで、 **[削除]** を選択します。

    ![[削除] の選択](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **[デバイスの削除]** ブレードで、削除するデバイスの名前を入力して **[削除]** を選択します。

    ![削除の確定](media/azure-stack-edge-return-device/delete-resource-2.png)

デバイスと関連付けられたリソースが正常に削除されたら、通知されます。

## <a name="get-a-replacement-device"></a>交換用デバイスを入手する

既存のデバイスにハードウェア障害があるかアップグレードが必要な場合、交換用デバイスが必要です。 デバイスにハードウェアの問題がある場合、次の手順を実行します。

1. [ハードウェアの問題のサポート チケットを開きます](#open-a-support-ticket)。 Microsoft サポートによって、このインスタンスにはフィールド交換ユニット (FRU) が利用できない、またはデバイスのハードウェア アップグレードが必要であると判断されます。 どちらの場合も、サポートは交換用デバイスを発注します。
2. 交換用デバイスのための[新しいリソースを作成](azure-stack-edge-deploy-prep.md#create-a-new-resource)します。 必ず、 **[I have a Azure Stack Edge device]\(Azure Stack Edge を持っています\)** チェック ボックスをオンにしてください。 
3. 交換用デバイスを受け取ったら、新しいリソースに対して交換用デバイスを[インストール](azure-stack-edge-deploy-install.md)および[アクティブ化](azure-stack-edge-deploy-connect-setup-activate.md)します。
4. 元のデバイスを返却するためのすべての手順に従います。
    1. 元のデバイスを返却するために、別のチケットを開きます。
    2. [デバイス上のデータを消去します](#erase-data-from-the-device)。
    3. [集荷をスケジュールします](#schedule-a-pickup)。
    5. 返却するデバイスに関連付けられている[リソースを削除します](#delete-the-resource)。



## <a name="next-steps"></a>次のステップ

- [帯域幅を管理する](azure-stack-edge-manage-bandwidth-schedules.md)方法について学習します。
