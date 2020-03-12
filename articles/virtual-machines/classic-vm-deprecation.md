---
title: 2023 年 3 月 1 日に Azure クラシック VM の提供が終了となります。
description: この記事では、クラシック VM の提供終了の概要を示します
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 9f3fd59fa040ab46a5fc4ef8272a17cba4c631b8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921746"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023 年 3 月 1 日までに IaaS リソースを Azure Resource Manager に移行する 

2014 年に Azure Resource Manager で IaaS を起動し、その後ずっと機能を強化してきました。 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) には現在、完全な IaaS 機能とその他の強化機能が含まれるため、2020 年 2 月 28 日に Azure Service Manager を介した IaaS VM の管理が非推奨となり、2023 年 3 月 1 日にこの機能の提供は完全に終了となります。 

現在、IaaS VM の約 90% で Azure Resource Manager が使用されています。 Azure Service Manager (ASM) を介して IaaS リソースを使用する場合は、今すぐ移行の計画を開始し、2023 年 3 月 1 日までに完了して [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/)をご利用ください。

クラシック VM は、[最新のライフサイクル ポリシー](https://support.microsoft.com/help/30881/modern-lifecycle-policy)に従って非推奨となります。

## <a name="how-does-this-affect-me"></a>どのような影響がありますか? 

1) 2020 年 2 月 28 日以降、2020 年 2 月中に Azure Service Manager (ASM) を介して IaaS VM をご利用にならなかったお客様は、クラシック VM を作成できなくなります。 
2) 2023 年 3 月 1 日に、お客様は Azure Service Manager を使用して IaaS VM を起動できなくなります。また、まだ実行中であるか、割り当て済みのものはすべて停止され、割り当てが解除されます。 
2) 2023 年 3 月 1 日に、Azure Resource Manager に移行していないサブスクリプションには、残っているクラシック VM の削除に関するタイムラインをお知らせします。  

次の Azure サービスと機能は、この提供終了の影響を**受けません**: 
- Cloud Services 
- クラシック VM で使用されて**いない**ストレージ アカウント 
- クラシック VM で使用されて**いない**仮想ネットワーク (VNet)。 
- その他のクラシック リソース

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか? 

- Azure Resource Manager への移行の計画をすぐに開始します。 

- [Linux](./linux/migration-classic-resource-manager-plan.md) および [Windows](./windows/migration-classic-resource-manager-plan.md) のクラシック VM を Azure Resource Manager に移行する場合の[詳細について学習](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)します。

- 詳細については、「[クラシックから Azure Resource Manager への移行に関してよく寄せられる質問](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)」を参照してください

- 技術的な質問と問題については、[サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

- FAQ に含まれていないその他の質問およびフィードバックについては、下にコメントを残してください。
