---
title: Azure Stack Edge デバイスの返却
description: データをワイプして Azure Stack Edge デバイスを返却してから、そのデバイスに関連付けられているリソースを削除する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/28/2021
ms.author: alkohli
ms.openlocfilehash: b17b613c07430ce7d4a86e251a86a96d250f0255
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469323"
---
# <a name="return-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスを返却する

[!INCLUDE [applies-to-pro-fpga](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

この記事では、データをワイプして、ご利用の Azure Stack Edge デバイスを返却する方法について説明します。 デバイスを返却した後、デバイスと関連付けられたリソースを削除することもできます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * デバイス上のデータ ディスクのデータを削除する
> * Azure portal でデバイスの返却を開始する
> * デバイスを選択して集荷をスケジュールする
> * Azure portal でリソースを削除する

## <a name="erase-data-from-the-device"></a>デバイスからデータを消去する

デバイス上のデータ ディスクのデータを削除するには、ご利用のデバイスをリセットする必要があります。

リセットする前に、必要に応じて、デバイス上のローカル コピーを作成します。 デバイスから Azure Storage コンテナーにデータをコピーできます。 

デバイスがリセットされる前でも、デバイスの返却を開始できます。

ローカル Web UI または PowerShell でデバイスをリセットできます。 PowerShell の手順については、「[デバイスをリセットする](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)」を参照してください。

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - 新しいデバイスに交換またはアップグレードする場合は、新しいデバイスを受け取ってからご利用のデバイスをリセットすることをお勧めします。
> - デバイスのリセットでは、デバイスからすべてのローカル データが削除されるだけです。 クラウド内にあるデータは削除されることなく、[料金](https://azure.microsoft.com/pricing/details/storage/)がかかります。 このデータは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのクラウド ストレージ管理ツールを使用して、個別に削除する必要があります。

## <a name="initiate-device-return"></a>デバイスの返却を開始する

返却プロセスを開始するには、次の手順を行います。

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (プレビュー)](#tab/azure-edge-hardware-center) 

Azure Edge Hardware Center を使用してデバイスを注文した場合は、次の手順に従ってデバイスを返却します。

1. Azure portal で、Azure Edge Hardware Center の注文品目のリソースに移動します。 **[概要]** で、右側のウィンドウの上部のコマンド バーに移動し、 **[返却]** を選択します。 返却オプションは、デバイスを受け取った後にのみ有効になります。

    ![デバイスの返却 1](media/azure-stack-edge-return-device/hardware-center-return-device-1.png)  

1. **[ハードウェアの返却]** ブレードで次の情報を指定します。

    ![デバイスの返却 2](media/azure-stack-edge-return-device/hardware-center-return-device-2.png) 

    1. ドロップダウン リストから、 **[返品理由]** を選択します。

    1. デバイスのシリアル番号を指定します。 デバイスのシリアル番号を取得するには、デバイスのローカル Web UI に移動し、 **[概要]** に移動します。  
    
       ![デバイスのシリアル番号 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    1. (省略可能) **サービス タグ** 番号を入力します。 サービス タグ番号は、デバイスに固有の 5 文字以上の識別子です。 サービス タグは、デバイスの (デバイスに向かって) 右下隅にあります。 情報タグ (スライド式のラベル パネル) を引き出します。 このパネルには、サービス タグ、NIC、MAC アドレスなどのシステム情報が含まれています。 
    
       ![サービス タグ番号 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    1. 返送用ボックスを要求するには、 **[ハードウェア ユニットを返品するために配送ボックスが必要]** をオンにします。 **[Need an empty box to return]\(返却するために空き箱が必要\)** の質問に **[はい]** と回答します。
    
    1. **プライバシー条項** を確認し、確認してプライバシー条項に同意したという注記の横にあるチェックボックスをオンにします。

    1. **[集荷の詳細]** を確認します。 既定では、これらは配送先住所に設定されます。 新しい住所を追加するか、保存されている住所とは別の住所を返却集荷に選択できます。

        ![デバイスの返却 3](media/azure-stack-edge-return-device/hardware-center-return-device-3.png) 

    1. **[Initiate return]\(返却の開始\)** を選択します。

1. 返却要求が送信されると、注文品目のリソースには、返品発送の状態が反映されるようになります。 状態は、 **[返品開始]** から **[集荷]** 、 **[返品完了]** に進みます。 ポータルを使用して、リソースの返却状態をいつでも確認できます。

    ![デバイスの返却 5](media/azure-stack-edge-return-device/hardware-center-return-device-4.png) 

1. 要求が開始されると、デバイスの集荷のスケジュールを設定できるように、Azure Stack Edge 運用チームから連絡します。

次の手順では、デバイスを梱包します。


### <a name="portal-classic"></a>[ポータル (クラシック)](#tab/azure-portal)

クラシック ポータルを使用してデバイスを注文した場合は、次の手順に従ってデバイスを返却します。

1. Azure portal で、お使いの Azure Stack Edge リソースに移動します。 **[概要]** で、右ペインのコマンド バーに移動し、 **[デバイスの返却]** を選択します。 

    ![デバイスの返却 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. **[デバイスの返却]** ブレードの **[基本的な詳細]** で:

    1. デバイスのシリアル番号を指定します。 デバイスのシリアル番号を取得するには、デバイスのローカル Web UI に移動し、 **[概要]** に移動します。  
    
       ![デバイスのシリアル番号 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. サービス タグ番号を入力します。 サービス タグ番号は、デバイスに固有の 5 文字以上の識別子です。 サービス タグは、デバイスの (デバイスに向かって) 右下隅にあります。 情報タグ (スライド式のラベル パネル) を引き出します。 このパネルには、サービス タグ、NIC、MAC アドレスなどのシステム情報が含まれています。 
    
       ![サービス タグ番号 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. ドロップダウン リストから、返却の理由を選択します。

       ![デバイスの返却 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. **[Shipping details]\(発送元の詳細\)** の下で:

    1. ご自分の氏名、会社名、および会社の完全な住所を入力します。 勤務先電話番号 (市外局番を含む) と通知用の電子メール ID を入力します。
    2. 返送用ボックスが必要な場合は、要求することができます。 **[Need an empty box to return]\(返却するために空き箱が必要\)** の質問に **[はい]** と回答します。

    ![デバイスの返却 3](media/azure-stack-edge-return-device/return-device-3.png)

4. **プライバシー条項** を確認し、確認してプライバシー条項に同意したという注記の横にあるチェックボックスをオンにします。

5. **[Initiate return]\(返却の開始\)** を選択します。

    ![デバイスの返却 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. デバイスの返却の詳細がキャプチャされたら、Azure Stack Edge 運用チームにメールで通知できます。 メール アプリケーションがインストールされ、構成されていることを前提として、メール アプリケーションを使用できます。 また、データをコピーして作成し、メールを送信することもできます。

    ![デバイスの返却 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge 運用チームがメールを受信すると、返送ラベルが送られてきます。 このラベルを受け取ったら、運送業者とデバイスの集荷をスケジュールできます。 

---

## <a name="pack-the-device"></a>デバイスのパック

デバイスをパックするには、次の手順を実行します。

1. デバイスをシャットダウンします。 ローカル Web UI で、 **[メンテナンス] > [Power settings]\(電源設定\)** に移動します。
2. **[シャットダウン]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして続行します。 詳細については、「[電源を管理する](../databox-online/azure-stack-edge-manage-access-power-connectivity-mode.md#manage-power)」を参照してください。
3. 電源ケーブルを外して、デバイスからネットワーク ケーブルをすべて取り外します。
4. 次の図に示すように、次の手順に従って、出荷パッケージを慎重に準備します。

    ![デバイスの梱包](media/azure-stack-edge-return-device/device-packaging-1.svg) 

    1. Azure から要求された出荷箱、または元の出荷箱をパッケージ梱包材と一緒に使用します。 
    1. 下部梱包材を箱の中に配置します。
    1. 梱包材の上にデバイスを配置し、このときに、梱包材の中にぴったり収まるようにします。
    1. パッケージに上部梱包材を配置します。
    1. アクセサリ トレイに電源コードを配置し、上部梱包材の上にレールを配置します。
    1. 箱に封をし、Azure から受け取った出荷ラベルをパッケージに貼り付けます。
   
    > [!IMPORTANT]
    > 返送品を準備するための適切なガイドラインが確認されない場合、デバイスが破損する可能性があり、破損したデバイスの料金が課される可能性があります。 [製品のサービス使用条件](https://www.microsoft.com/licensing/product-licensing/products)に関するページと[紛失または破損しているデバイスに関する FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) に関するページを参照してください。
 


## <a name="schedule-a-pickup"></a>集荷をスケジュールする

集荷をスケジュールするには、次の手順を行います。

1. リージョンの運送業者で集荷をスケジュールします。 米国でデバイスを返却する場合、UPS または FedEx の運送業者をご利用になれます。 UPS に集荷をスケジュールするには:

    1. 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    2. 電話で、印刷ラベルに表示されている返送追跡番号を伝えます。
    3. 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Azure Stack Edge を持ち込むこともできます。

## <a name="complete-return"></a>返却を完了する

このセクションでは、返却が完了した時点を確認し、注文の削除を選択できます。 

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (プレビュー)](#tab/azure-edge-hardware-center)

デバイスが Azure データセンターに到着した後、破損や改ざんの兆候がデバイスにないかを検証します。

- デバイスがそのまま良好な状態で到着した場合、課金メーターはそのリソースに対して停止されます。 Azure Stack Edge 運用チームが、デバイスが返却されたことを確認するためにご連絡します。 注文品目の状態には、返却が完了として反映されます。  これで、Azure portal で、注文品目のリソースを削除することができます。
- デバイスが大幅に破損して到着した場合、料金が課される可能性があります。 詳細については、[紛失または破損しているデバイスに関する FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) に関するページと[製品のサービス使用条件](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。 

### <a name="portal-classic"></a>[ポータル (クラシック)](#tab/azure-portal) 

デバイスが Azure データセンターに到着した後、デバイスは破損や改ざんの兆候がないかを検証されます。

- デバイスがそのまま良好な状態で到着した場合、課金メーターはそのリソースに対して停止されます。 Azure Stack Edge 運用チームが、デバイスが返却されたことを確認するためにご連絡します。 Azure portal でデバイスと関連付けられたリソースを削除することを選択できます。
- デバイスが大幅に破損して到着した場合、料金が課される可能性があります。 詳細については、[紛失または破損しているデバイスに関する FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) に関するページと[製品のサービス使用条件](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。  


次の場合に Azure portal でデバイスを削除できます。

- 注文後、デバイスが Microsoft によって準備される前。
- デバイスを Microsoft に返送し、Azure Stack Edge 運用チームからデバイスが返されたことを確認する連絡があった後。 返されたデバイスが Azure データセンターでの物理的な検査に合格するまで、運用チームが連絡することはありません。

別のサブスクリプションまたは場所に対してデバイスを有効にしている場合、Microsoft によって 1 営業日以内に新しいサブスクリプションまたは場所に注文が移動されます。 注文が移動された後、このリソースを削除できます。


Azure portal でデバイスとリソースを削除するには、次の手順を行います。

1. Azure portal で自分のリソースに移動し、 **[概要]** に移動します。 コマンド バーで、 **[削除]** を選択します。

    ![[削除] の選択](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **[デバイスの削除]** ブレードで、削除するデバイスの名前を入力して **[削除]** を選択します。

    ![削除の確定](media/azure-stack-edge-return-device/delete-resource-2.png)

デバイスと関連付けられたリソースが正常に削除されたら、通知されます。

---

## <a name="next-steps"></a>次の手順

- [Azure Stack Edge デバイスを交換する](azure-stack-edge-replace-device.md)方法について説明します。