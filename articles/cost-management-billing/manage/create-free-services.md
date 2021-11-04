---
title: Azure 無料アカウントを使用して無料サービスを作成する
description: Azure の無料アカウントに含まれるサービスを作成する方法を説明します。 これらのサービスは、利用可能な任意のリージョンで作成できます。
author: bandersmsft
ms.reviewer: amberrb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 34a176c4365f124029149a9663ea1a13409064a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457964"
---
# <a name="create-services-included-with-azure-free-account"></a>Azure の無料アカウントに含まれるサービスを作成する

Azure の無料アカウント作成後、最初の 30 日間は、サードパーティの Marketplace での購入を除き、すべてのサービスに使用できる 200 ドルのクレジットが請求通貨で付与されます。 無料のクレジットを使用して、Azure サービスのさまざまなレベルと種類を実際に使ってみることができます。 この期間中に無料ではないサービスや Azure リソースを使用すると、クレジットから料金が差し引かれます。

最初の 30 日の終わりまでにクレジットを使い切らなかった場合は、失われます。 最初の 30 日が過ぎてからサインアップ後最大 12 か月間使用できるのは、容量が制限された "*一部のサービス*" のみです。すべての Azure サービスが無料になるわけではありません。 30 日が経過する前にアップグレードし、クレジットが残っている場合、残りの日数の間、従量課金制サブスクリプションでクレジットの残りを使用できます。 たとえば、11 月 1 日に無料アカウントにサインアップし、11 月 5 日にアップグレードした場合、11 月 30 日までは新しい従量課金制サブスクリプションでクレジットを使用することができます。 

Azure の無料アカウントには、12 か月間 "*指定された容量*" が無料になるサービスと、常時無料の一連のサービスが含まれています。 無料アカウントでは、一部のサービス レベルのみを特定の容量内で利用できます。 たとえば、Azure にはさまざまなニーズに対応した数多くの仮想マシンが用意されています。 無料アカウントが無料でアクセスできる VM は 1 種類しかありません。これは月あたり最大 750 時間まで使用できるバースト可能な B シリーズの B1S です。 制限内で無料アカウントを利用することにより、さまざまな構成で無料のサービスを使用できます。 Azure の無料アカウントと無料で利用可能な製品の詳細については、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。

## <a name="create-free-services-in-the-azure-portal"></a>Azure Portal で無料サービスを作成する

無料サービスの作成には、Azure portal の[無料サービス ページ](https://go.microsoft.com/fwlink/?linkid=859151)を使用することをお勧めします。 または、[Azure portal](https://portal.azure.com) にサインインして「**無料サービス**」を検索してください。 無料サービス ページ以外でリソースを作成した場合、既定では無料レベルまたは無料リソースの構成オプションが常に選択されるとは限りません。 料金を回避するには、必ず無料サービス ページからリソースを作成してください。 また、リソースを作成する際は、無料のレベルを選択するようにしてください。

![無料サービス ページを示すスクリーンショット](./media/create-free-services/billing-freeservices-grid.png)

## <a name="services-can-be-created-in-any-region"></a>サービスは任意の地域で作成可能

制限の範囲内であれば、サービスが使用可能な任意の地域で無料サービスを作成できます。 たとえば、Azure の無料アカウントでは、B1S Windows 仮想マシンを毎月 750 時間、無料で使用できます。 この仮想マシンは、B シリーズの仮想マシンが使用可能な地域なら、どこでも作成できます。 750 時間を超えない限り、Azure で課金されることはありません。 たとえば、米国のお客様が、西ヨーロッパで B1S Windows 仮想マシンをプロビジョニングし、750 時間まで無料で使用する、ということが可能です。

リージョンごとで使用可能な Azure サービスの詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。

## <a name="create-multiple-service-instances-in-allowed-limits"></a>許容限度内で複数のサービス インスタンスを作成する

合計が使用制限内であれば、サービスの複数のインスタンスを無料で作成できます。 たとえば、お使いの Azure の無料アカウントで、B1S Windows 仮想マシンを無料で毎月 750 時間、使用するとします。 その 750 時間は、必要な任意の組み合わせで使用できます。 B1S Windows 仮想マシンを 5 つ作成し、それぞれ 150 時間ずつ使用することができます。

## <a name="next-steps"></a>次のステップ

- [Azure 無料アカウントに含まれている無料サービスの使用状況を確認する](check-free-service-usage.md)方法を学習します。
- [Azure 無料アカウントの課金を回避](avoid-charges-free-account.md)する方法を学習します。
