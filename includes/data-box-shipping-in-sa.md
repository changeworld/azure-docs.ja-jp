---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: 36396088c6036985d0bbdd57c7bb5b7b235edbda
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247281"
---
南アフリカでデバイスを返送するには、次の手順を行います。

1. 返送するデバイスを元の箱に梱包します。
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 追跡番号 (Data Box のローカル Web UI の **[発送準備]** ページに参照番号として表示されます) をメモしておきます。 追跡番号は、"発送準備" の手順が正常に完了した後に提供されます。 このページから配送先住所ラベルをダウンロードし、梱包箱に貼り付けます。
4. Azure Data Box Operations に返送コードを要求します。 パッケージをデータセンターに返送するには、返送コードが必要です。 [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送信します。 配送先住所ラベルの返送先住所の横に、はっきりと見えるようにこのコードを記載します。
5. 次のいずれかの方法を使用して、DHL で集荷を予約します。
 
   * [DHL Express South Africa **の「** Schedule a Pickup (集荷をスケジュールする)](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference)」にアクセスして、オンラインで集荷を予約します。
   * 次のテンプレートを使用して、電子メールを [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) に送信します。

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * または、最寄りの DHL サービス ポイントにパッケージを持ち込みます。

6. 問題が発生した場合は、問題の詳細を記載した電子メールを [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) に送ります。件名には貨物運送状番号を含めます。 +27(0)119213902 に電話することもできます。