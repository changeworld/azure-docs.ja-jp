---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 87e6c096c0ff0e58e8acebc13a62e44ea7d65c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181234"
---
StorSimple Cloud Appliance を使用する際は、セキュリティに関する次の考慮事項に留意してください。

* このクラウド アプライアンスは Microsoft Azure サブスクリプションを通じてセキュリティで保護されます。 つまり、このクラウド アプライアンスを使用している場合に Azure サブスクリプションが侵害されると、クラウド アプライアンス上に格納されたデータも影響を受けます。
* StorSimple に格納されたデータの暗号化に使われる証明書の公開キーは、Azure Portal に対して安全に公開され、秘密キーは StorSimple Cloud Appliance 上に保持されます。 StorSimple Cloud Appliance では、公開キーと秘密キーの両方が Azure に格納されます。
* クラウド アプライアンスは、Microsoft Azure データセンターでホストされます。

