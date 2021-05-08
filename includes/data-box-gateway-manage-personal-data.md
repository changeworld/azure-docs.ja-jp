---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ebd0e48c9f197439e838efbc936537ca5da3520f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581332"
---
- **注文の詳細**。 注文が作成されると、ユーザーの配送先住所、メール アドレス、および連絡先情報が Azure Portal に格納されます。 たとえば、次の情報が保存の対象となります。
  - 連絡先の名前
  - 電話番号
  - 電子メール アドレス
  - 番地
  - City
  - ZIP コード/郵便番号
  - 状態
  - 国/地方/地域
  - 配送追跡番号

    注文の詳細は暗号化され、サービスに格納されます。 このサービスは、ユーザーがリソースまたは注文を明示的に削除するまで情報を保持します。 リソースとそれに対応する注文の削除は、デバイスが発送された時点から、そのデバイスが Microsoft に戻るまでブロックされます。

- **配送先住所**。 注文を受けると、Data Box サービスによって、配送先住所が UPS などのサードパーティの運送業者に提供されます。

- **共有ユーザー**。 デバイス上のユーザーはまた、共有に格納されたデータにもアクセスできます。 共有データにアクセスできるユーザーの一覧を表示できます。 共有が削除されると、この一覧も削除されます。