---
title: Azure Data Box ポータル管理ガイド | Microsoft Docs
description: Azure portal を使用して Azure Data Box を管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/19/2018
ms.author: alkohli
ms.openlocfilehash: 1b228a66f2d59b3ff252df266783f7bd5d27139e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645441"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Azure portal を使用して Data Box を管理する

この記事では、Data Box で実行できる一部の複雑なワークフローと管理タスクについて説明します。 Data Box の管理は、Azure portal またはローカル Web UI から行うことができます。 

この記事では、Azure portal を使用して実行できるタスクについて説明します。 Azure portal を使用して、注文を管理したり、Data Box を管理したり、完了まで注文の状態を追跡したりします。


## <a name="cancel-an-order"></a>注文のキャンセル

発注後、さまざまな理由から注文をキャンセルしなければならないことがあります。 キャンセルできるのは、処理される前の注文だけです。 注文が処理されて Data Box が準備された後は、注文をキャンセルできません。 

注文をキャンセルするには、次の手順を実行します。

1.  **[概要] > [キャンセル]** に移動します。 

    ![注文のキャンセル 1](media/data-box-portal-admin/cancel-order1.png)

2.  注文のキャンセル理由を入力します。  

    ![注文のキャンセル 2](media/data-box-portal-admin/cancel-order2.png)

3.  注文のキャンセル後、ポータルで注文の状態が更新され、**取り消し済み**として表示されます。 

## <a name="clone-an-order"></a>注文の複製

特定の状況では複製機能が役立ちます。 たとえば、ユーザーが Data Box を使って何らかのデータを転送済みだとします。 その後、生成されるデータが増えていくと、それを Azure に転送するために別の Data Box が必要になります。 このようなケースでは、同じ注文をそのまま複製することができます。

注文を複製するには、次の手順を実行します。

1.  **[概要] > [複製]** に移動します。 

    ![注文の複製 1](media/data-box-portal-admin/clone-order1.png)

2.  注文の詳細はすべてそのままの状態で維持されます。 複製された注文は、元の名前の最後に "*-Clone*" を追加した名前になります。 プライバシー情報を確認済みであることへの同意として、チェック ボックスをオンにします。 **Create** をクリックしてください。

複製は数分で作成され、ポータルに新しい注文が反映されます。


## <a name="delete-order"></a>注文の削除

注文が完了した時点で、その注文を削除したい場合があります。 注文には、お客様の名前、住所、連絡先情報など、個人情報が含まれています。 この個人情報は、注文が削除されると削除されます。

削除できるのは、完了済みまたはキャンセル済みの注文だけです。 注文を削除するには次の手順を実行します。

1. **[すべてのリソース]** に移動します。 目的の注文を検索します。

2. 削除する注文をクリックし、**[概要]** に移動します。 コマンド バーの **[削除]** をクリックします。

    ![Data Box 注文の削除 1](media/data-box-portal-admin/delete-order1.png)

3. 注文の削除を確認するメッセージが表示されたら注文の名前を入力します。 **[削除]** をクリックします。

## <a name="download-shipping-label"></a>配送先住所ラベルのダウンロード

Data Box の E インク表示が動作しておらず、返送ラベルが表示されない場合は、配送先住所ラベルをダウンロードしなければならないことがあります。 

配送先住所ラベルをダウンロードするには、次の手順を実行します。

1.  **[概要] > [出荷ラベルをダウンロード]** に移動します。 このオプションは、デバイスの発送後にのみ選択できます。 

    ![配送先住所ラベルのダウンロード](media/data-box-portal-admin/download-shipping-label.png)

2.  次の返送ラベルがダウンロードされます。 ラベルを保存し、印刷します。ラベルを折りたたみ、デバイスのクリア カバーに挿入します。 ラベルが見えることを確認します。 以前の出荷で使用したステッカーをデバイスからはがします。

    ![配送先住所ラベルの例](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>配送先住所の編集

発注後に配送先住所の編集が必要になることも考えられます。 編集できるのは、デバイスが発送されるまでとなります。 デバイスの発送後は、このオプションは使用できなくなります。

注文を編集するには、次の手順を実行します。

1. **[注文の詳細] > [配送先住所の編集]** に移動します。

    ![配送先住所の編集 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. 配送先住所を編集して検証し、変更を保存します。

    ![配送先住所の編集 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>通知の詳細の編集

注文の状態を知らせるメールの宛先となるユーザーを変更しなければならないことがあります。 たとえばデバイスが発送または集荷されたときに、あるユーザーに連絡する必要があるとしましょう。 一方、データのコピーが完了したときの連絡先は、別のユーザーにする必要があります。Azure Storage アカウントにデータが存在することを別のユーザーが確認したうえで、コピー元からデータを削除するためです。 このような場合は、通知の詳細を編集することができます。

通知の詳細を編集するには、次の手順を実行します。

1. **[注文の詳細] > [通知の詳細の編集]** に移動します。

    ![通知の詳細の編集 1](media/data-box-portal-admin/edit-notification-details1.png)

2. これで通知の詳細を編集し、変更を保存できるようになります。
 
    ![通知の詳細の編集 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>注文履歴のダウンロード

Data Box の注文が完了すると、デバイス ディスク上のデータは消去されます。 デバイスのクリーンアップが完了したら、Azure portal で注文履歴をダウンロードできます。

注文履歴をダウンロードするには、次の手順を実行します。

1. Data Box の注文で、**[概要]** に移動します。 注文が完了していることを確認します。 注文が完了し、デバイスのクリーンアップが完了している場合は、**[注文の詳細]** に移動します。 **[注文履歴のダウンロード]** オプションが使用可能になっています。

    ![注文履歴のダウンロード](media/data-box-portal-admin/download-order-history-1.png)

2. **[注文履歴のダウンロード]** をクリックします。 ダウンロードした履歴には、運送業者の追跡ログの記録が含まれています。 このログの一番下までスクロールすると、次の項目へのリンクが表示されます。
    
    - **コピー ログ** - Data Box から Azure Storage アカウントへのデータのコピー中にエラーが出力されたファイルのリストが含まれています。
    - **監査ログ** - Data Box が Azure データセンターの外部にあるときの Data Box の電源投入と共有へのアクセスに関する情報が含まれています。
    - **BOM ファイル** - **発送の準備**中にダウンロードでき、ファイル名、ファイル サイズ、およびファイル チェックサムを持つファイルの一覧 (ファイル マニフェストとも呼ばれます) が含まれています。

        ```
        -------------------------------
        Microsoft Data Box Order Report
        -------------------------------
        
        Name                                               : eastusdryrun                                      
        StartTime(UTC)                                     : 9/6/2018 12:54:47 PM +00:00                       
        DeviceType                                         : ImolaPod                                          
        
        -------------------
        Data Box Activities
        -------------------
        
        Time(UTC)             | Activity                       | Status          | Description                                                                                                                                           
        
        9/6/2018 12:54:51 PM  | OrderCreated         | Completed  |                                                                                                                              
        9/11/2018 8:57:38 PM  | DevicePrepared       | Completed  |                                                                                                                                                       
        9/12/2018 7:28:15 PM  | ShippingToCustomer   | InProgress | Pickup Scan. Local Time : 9/12/2018 2:52:31 PM at Chantilly                                                                                           
        9/13/2018 2:33:04 AM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/12/2018 9:00:00 PM at Chantilly                                                                                                                                                                                                                                                              
        9/13/2018 12:40:31 PM | ShippingToCustomer   | InProgress | Arrival Scan. Local Time : 9/13/2018 5:00:00 AM at Oakland                                                                                            
        9/13/2018 2:42:10 PM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/13/2018 6:08:00 AM at Oakland                                                                                          
        9/13/2018 3:42:12 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:14:08 AM at Sunnyvale                                                                                      
        9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:56:54 AM at Sunnyvale                                                                                      
        9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Out For Delivery Today. Local Time : 9/13/2018 9:11:21 AM at Sunnyvale                                                                                
        9/13/2018 5:43:07 PM  | ShippingToCustomer   | Completed  | Delivered. Local Time : 9/13/2018 9:44:17 AM at SUNNYVALE                                                                                             
        9/14/2018 11:48:35 PM | ShippingToDataCenter | InProgress | Pickup Scan. Local Time : 9/14/2018 3:55:37 PM at Sunnyvale                                                                                                                                                                                 
        9/15/2018 1:52:35 AM  | ShippingToDataCenter | InProgress | Arrival Scan. Local Time : 9/14/2018 6:31:00 PM at San Jose                                                                                           
        9/15/2018 2:52:39 AM  | ShippingToDataCenter | InProgress | Departure Scan. Local Time : 9/14/2018 7:17:00 PM at San Jose                                                                                                                                                                             
        9/17/2018 8:23:31 AM  | ShippingToDataCenter | InProgress | Destination Scan. Local Time : 9/17/2018 4:14:37 AM at Chantilly                                                                                      
        9/17/2018 12:24:42 PM | ShippingToDataCenter | InProgress | Loaded on Delivery Vehicle. Local Time : 9/17/2018 7:45:36 AM at Chantilly                                                                            
        9/17/2018 1:25:11 PM  | ShippingToDataCenter | InProgress | Out For Delivery Today. Local Time : 9/17/2018 8:27:11 AM at Chantilly                                                                                
        9/17/2018 2:25:51 PM  | ShippingToDataCenter | Completed | Delivered. Local Time : 9/17/2018 9:56:32 AM at STERLING                                                                                              
        9/18/2018 9:55:41 PM  | DeviceBoot           | Completed | Appliance booted up successfully                                                                                                                      
        9/18/2018 11:00:25 PM | DataCopy             | Started   |                                                                                                                                                       
        9/18/2018 11:01:33 PM | DataCopy             | Completed | Copy Completed.                                                                                                                                       
        9/18/2018 11:20:58 PM | SecureErase          | Started   |                                                                                                                                                       
        9/18/2018 11:28:46 PM | SecureErase          | Completed | Azure Data Box:BY506B4B616700 has been sanitized according to NIST 800 -88 Rev 1.                                                                     
        
        ----------------------
        Data Box Job Log Links
        ----------------------
        
        Account Name         : eastusdryrun                                         
        Copy Logs Path       : copylog/copylogd695869a2a294396b7b903296c208388.xml                                                                                                                                                     
        Audit Logs Path      : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700                                                                                                                     
        BOM Files Path       : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700
        ```
次に、ストレージ アカウントに移動し、コピー ログを表示できます。

![ストレージ アカウント内のログ](media/data-box-portal-admin/logs-in-storage-acct-2.png)

また、監査ログと BOM ファイルを含む生産物流管理ログを表示することもできます。

![ストレージ アカウント内のログ](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>注文の状態の確認

ポータル内でデバイスの状態が変化すると、メールで通知されます。

|注文の状態 |説明 |
|---------|---------|
|注文済み     | 正常に発注されました。 <br>デバイスが使用可能な場合、Microsoft は出荷用のデバイスを特定し、デバイスを準備します。 <br> デバイスがただちに入手できない場合、デバイスが入手可能になったときに注文が処理されます。 注文の処理は数日から 2 ～ 3 か月かかることがあります。 注文を 90 日以内に満たすことができない場合、注文が取り消されて通知されます。         |
|処理済み     | 注文処理は完了しています。 注文に応じて、デバイスを出荷する準備がデータ センター内で行われます。         |
|出荷しました     | 注文は発送済みです。 出荷を追跡するには、ポータルの注文に表示されている追跡 ID を使用します。        |
|配信済み     | 出荷は、注文に指定された住所に配達済みです。        |
|集荷されました     |返送する荷物が運送業者によって集荷されてスキャンされました。         |
|受取済み     | デバイスは Azure データ センターで受け取られてスキャンされます。 <br> 出荷が検査されると、デバイスのアップロードが開始されます。      |
|データのコピー     | データのコピーが進行中です。 Azure portal で、注文のコピーの進行状況を追跡します。 <br> データのコピーが完了するまでお待ちください。 |
|完了       |注文が正常に完了しました。<br> オンプレミスのデータをサーバーから削除する前に、データが Azure に存在することを確認してください。         |
|完了 (エラーあり)| データのコピーが完了しましたが、コピー中にエラーが発生しました。 <br> Azure portal に示されているパスを使用してコピー ログを確認してください。   |
|Canceled            |注文はキャンセルされました。 <br> お客様によって注文がキャンセルされたか、またはエラーが発生したために、サービスによって注文がキャンセルされました。 注文を 90 日以内に満たすことができない場合も、注文が取り消されて通知されます。     |
|クリーンアップ | デバイスディスク上のデータは消去されます。 デバイスのクリーンアップは、注文履歴が Azure portal でダウンロード可能なときに完了したと見なされます。|



## <a name="next-steps"></a>次の手順

- [Data Box の問題のトラブルシューティング](data-box-faq.md)に関するページを参照してください。
