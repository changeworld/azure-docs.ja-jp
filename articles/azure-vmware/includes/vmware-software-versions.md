---
title: VMware ソフトウェアのバージョン
description: Azure VMware Solution でサポートされている VMware ソフトウェアのバージョン。
ms.topic: include
ms.date: 07/20/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: c80aed722a58fd8178e78d1ea98b888abdd8b830
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122228956"
---
<!-- Used in faq.md and concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management -->


Azure VMware Solution のプライベート クラウドのクラスターの新しいデプロイで使用される VMware ソフトウェアのバージョンは次のとおりです。

| ソフトウェア              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3l   | 
| ESXi                  |    6.7 U3l   | 
| vSAN                  |    6.7 U3l   |
| HCX                   |    4.1       |
| NSX-T <br />**注意:** NSX-T が NSX のサポートされている唯一のバージョンです。               |      [[!INCLUDE [nsxt-version](nsxt-version.md)]](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)     |


既存のプライベート クラウドに追加された新しいクラスターに、現在実行中のソフトウェア バージョンが適用されます。 詳細については、[VMware ソフトウェアの要件](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)に関するページを参照してください。


