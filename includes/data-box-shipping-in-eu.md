---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/29/2021
ms.author: alkohli
ms.openlocfilehash: 0ce47d9442f9b9bb7625b99e5544af158e1351ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449977"
---
ヨーロッパでデバイスを返送するには、次の手順を実行します。

1. 必ずデバイスの電源をオフにてケーブルを取り外します。
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 配送先住所ラベルが電子インク ディスプレイに表示されていることを確認し、運送業者の集荷をスケジュールします。 電子インク ディスプレイのラベルが破損している、なくなっている、または表示されない場合は、[Microsoft サポートに](..\articles\databox\data-box-disk-contact-microsoft-support.md)ご連絡ください。 サポートから指示された場合は、Azure portal の **[概要] > [出荷ラベルをダウンロード]** にアクセスしてください。 配送先住所ラベルをダウンロードして、デバイスに貼り付けます。
1. **ドイツまたはスイスの Azure データセンターに返送する場合**、Azure データセンターでは、すべてのデバイス返却に関する事前通知が必要です。
    1. Azure Data Box Operations のアドレス [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送って、Inbound ID を受け取ります。 [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送信します。 次に示すテンプレートを使用します。

       ```
       To: adbops@microsoft.com
       Subject: Request for Azure Data Box Inbound ID: <orderName> 
       Body: 
        
       I am ready to return an Azure Data Box and would like to request an Inbound ID for the following order:
       
       Order Name: <orderName>
       Return Tracking Number: <returnTracking#>
       ```

    1. Azure Data Box Operations から提供される入荷 ID 番号を書き留め、返品ラベルの近くの見やすい場所に記入してください。
1. デバイスを返送する場合は、UPS での集荷をスケジュールします。 集荷のスケジュールを設定するには:

    * 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    * 電話で、電子インク ディスプレイまたは印刷ラベルに表示されている返送追跡番号を伝えます。 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。
    * 集荷のスケジュール中に問題が発生した場合や、追加料金の支払いを求められた場合は、Azure Data Box Operations にお問い合わせください。 [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送信します。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box を持ち込むこともできます。

4. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

