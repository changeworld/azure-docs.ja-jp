---
title: 標準契約 | Azure
description: Azure Marketplace と AppSource の標準契約
services: Azure, Marketplace, Compute, Storage, Networking
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/19/2019
ms.author: v-chjen
ms.openlocfilehash: dc8edf2b6e4a1204e7edd515da9323896049eb13
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228200"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商業マーケットプレースの標準契約

お客様の調達プロセスを簡素化し、ソフトウェア ベンダーの法的な複雑さを軽減するために、Microsoft では、マーケットプレースでの取引の促進に役立つ Microsoft 商業マーケットプレースの標準契約を用意しています。 商業マーケットプレース パブリッシャーは、カスタムの使用条件を作成するのではなく、標準契約の下で各自のソフトウェアを提供することを選択でき、お客様はそれを詳細に調べて 1 回同意するだけで済みます。 標準契約はこちらで見つけることができます[https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)。

オファーの使用条件は、パートナー センターまたはクラウド パートナー ポータルでオファーを作成するときに定義されます。 独自のカスタムの使用条件を提供する代わりに、Microsoft 商業マーケットプレースの標準契約を使用することを選択できます。

>[!Note]
>Microsoft 商業マーケットプレースの標準契約を使用してオファーを発行した後に、独自のカスタムの使用条件を使用することはできません。 これは、"または" のシナリオです。 ソリューションは、標準契約*または*独自の使用条件のどちらかの下で提供されます。 標準契約の使用条件を変更したい場合は、Standard Contract Amendments (標準契約の修正) を使用して行うことができます。

## <a name="standard-contract-amendments"></a>Standard Contract Amendments (標準契約の修正)

Standard Contract Amendments (標準契約の修正) を使用すると、発行元は簡単のために標準契約を選択し、各自の製品またはビジネスに合わせて使用条件をカスタマイズすることができます。 Microsoft 標準契約を既に確認し、同意している場合、お客様に必要なことは契約の修正の確認のみです。

商業マーケットプレース パブリッシャーが使用できる修正には、次の 2 種類があります。

* Universal Amendments (ユニバーサル修正):これらの修正は、すべてのお客様の標準契約に例外なく適用されます。 ユニバーサル修正は、購入フローにあるオファーのすべてのお客様に示されます。 お客様は、提供されるオファーを使用する前に、標準契約の使用条件とこの修正に同意する必要があります。

* Custom Amendments (カスタム修正):これらの修正は、Azure テナント ID を通して特定のお客様のみを対象にした、標準契約への特殊な修正です。 発行元は、対象にするテナントを選択できます。 カスタム修正の使用条件は、オファーの購入フローでこのテナントのお客様にのみ示されます。  お客様は、提供されるオファーを使用する前に、標準契約の使用条件とこの修正に同意する必要があります。

>[!Note]
>これらの 2 種類の修正は、互いに重なり合っています。 カスタム修正の対象となるお客様には、購入中に標準契約へのユニバーサル修正も示されます。

Microsoft 商業マーケットプレースの標準契約は、次のオファーの種類に対して利用できます。Azure アプリケーション (ソリューション テンプレートとマネージド アプリケーション)、仮想マシン、コンテナー、コンテナー アプリケーション、IoT Edge モジュール、および SaaS。

## <a name="customer-experience"></a>カスタマー エクスペリエンス

Azure マーケットプレースまたは AppSource での検出エクスペリエンス中に、お客様は、Microsoft 商業マーケットプレースの標準契約およびすべてのユニバーサル修正としてオファーに関連付けられている使用条件を確認できます。

![Azure portal のカスタマー検出エクスペリエンス。](media/marketplace-publishers-guide/azure-discovery-process.png)

Azure portal での購入プロセス中に、お客様は、Microsoft 商業マーケットプレースの標準契約およびすべてのユニバーサル修正またはテナント固有の修正としてオファーに関連付けられている使用条件を確認できます。

![Azure portal のカスタマー購入エクスペリエンス。](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

お客様は、オファーの使用条件を取得して同意するために Get-AzureRmMarketplaceTerms を使用できます。 標準契約とそれに関連付けられている修正は、このコマンドレットの出力で返されます。
