---
title: Azure portal で VM の FQDN を作成する
description: Azure portal で仮想マシン用に完全修飾ドメイン名 (FQDN) を作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351854"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM 用の Azure Portal での完全修飾ドメイン名の作成

[Azure ポータル](https://portal.azure.com)で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 この IP アドレスを使用して、VM にリモートでアクセスします。 ポータルでは[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) は作成されませんが、VM の作成後に追加できます。 この記事では、DNS 名または FQDN を作成する手順を示します。 

## <a name="create-a-fqdn"></a>FQDN の作成
この記事では、既に VM が作成されていることを前提としています。 必要に応じて、ポータルで [Linux](./linux/quick-create-portal.md) または [Windows](./windows/quick-create-portal.md) の VM を作成することができます。 VM が起動したら、次の手順を実行します。


1. ポータルで VM を選択します。 **[DNS 名]** で、 **[構成]** を選択します。
2. DNS 名を入力し、ページの上部にある **[保存]** を選択します。
3. VM の概要ブレードに戻るには、右上隅にある **[X]** を選択して、 **[構成]** ブレードを閉じます。 
4. *[DNS 名]* が正しく表示されていることを確認します。
   



## <a name="next-steps"></a>次のステップ
これで、VM がパブリック IP と DNS 名を持つようになったため、nginx、MongoDB、Docker などの、共通のアプリケーション フレームワークやサービスをデプロイできるようになりました。

Azure デプロイの構築に関するヒントについては、[Resource Manager の使用](../azure-resource-manager/management/overview.md)に関する記事から確認することもできます。

