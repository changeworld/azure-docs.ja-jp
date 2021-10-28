---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: 4d21a73b37f9a192ce080b0c5405b280b9100ba8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247335"
---
米国政府、日本、シンガポール、韓国、インド、南アフリカ、イギリス、ドイツ、スイス、西ヨーロッパ、オーストラリア、またはブラジルで Data Box をお使いの方で、ご自分の注文の作成時に自己管理による出荷を選択した場合は、こちらの手順に従います。

1. この手順が正常に完了したら、Data Box のローカル Web UI の **[発送準備]** ページに表示される承認コードをメモしておきます。
2. デバイスの電源をオフにして、ケーブルを取り外します。 デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. デバイスを返送する準備が整ったら、次のテンプレートを使用して Azure Data Box Operations チームにメールを送信します。

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a government-approved ID during the drop off.
    ```

   > [!NOTE]
   > - 返送に必要な情報は地域によって異なります。 
   > - ブラジルで Data Box を返す場合、[Azure Data Box の自己管理型の発送の使用](..\articles\databox\data-box-portal-customer-managed-shipping.md)に関するページで詳細な手順をご確認ください。 
