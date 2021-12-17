---
title: VMware ソフトウェアのバージョン
description: Azure VMware Solution でサポートされている VMware ソフトウェアのバージョン。
ms.topic: include
ms.date: 07/20/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1c773d723e9d43ee8a4ad0335c569dbf2e1f6388
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855006"
---
<!-- Used in faq.md and concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management -->


Azure VMware Solution のプライベート クラウドのクラスターの新しいデプロイで使用される VMware ソフトウェアのバージョンは次のとおりです。

| ソフトウェア              |    Version   |
| :---                  |     :---:    |
| vCenter               |    6.7 U3o   | 
| ESXi                  |    6.7 P05   | 
| vSAN                  |    6.7 P05   |
| HCX                   |    4.1       |
| NSX-T <br />**注意:** NSX-T が NSX のサポートされている唯一のバージョンです。               |      [[!INCLUDE [nsxt-version](nsxt-version.md)]](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)     |


既存のプライベート クラウドに追加された新しいクラスターに、現在実行中のソフトウェア バージョンが適用されます。 詳細については、[VMware ソフトウェアの要件](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)に関するページを参照してください。


