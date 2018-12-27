---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165737"
---
<!--v-sharos 10/13/2105 virtual device security-->

StorSimple 仮想デバイスを使用する場合、セキュリティに関する次の考慮事項に留意してください。

* 仮想デバイスは Microsoft Azure サブスクリプションを通じてセキュリティで保護されます。 つまり、仮想デバイスを使用している場合に Azure サブスクリプションが侵害されると、仮想デバイス上に格納されたデータも影響を受けます。
* Azure StorSimple に格納されたデータの暗号化に使われる証明書の公開キーは、Azure クラシック ポータルに対して安全に公開され、秘密キーは StorSimple デバイス上に保持されます。 StorSimple 仮想デバイスでは、公開キーと秘密キーの両方が Azure に格納されます。
* 仮想デバイスは、Microsoft Azure データセンターでホストされます。

