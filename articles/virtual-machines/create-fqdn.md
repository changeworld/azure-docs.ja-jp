---
title: Azure portal で VM の FQDN を作成する
description: Azure portal で仮想マシン用に完全修飾ドメイン名 (FQDN) を作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b010070b7a45c24037c6de4648574c01b017d759
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107399"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM 用の Azure Portal での完全修飾ドメイン名の作成

[Azure ポータル](https://portal.azure.com)で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 この IP アドレスを使用して、VM にリモートでアクセスします。 ポータルでは[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) は作成されませんが、VM の作成後に追加できます。 この記事では、DNS 名または FQDN を作成する手順を示します。 

## <a name="create-a-fqdn"></a>FQDN の作成
この記事では、既に VM が作成されていることを前提としています。 必要に応じて、ポータルで [Linux](./linux/quick-create-portal.md) または [Windows](./windows/quick-create-portal.md) の VM を作成することができます。 VM が起動したら、次の手順を実行します。


1. ポータルで VM を選択します。 
1. 左側のメニューで **[プロパティ]** を選択します。
1. **[パブリック IP アドレス/DNS 名ラベル]** で、使用する IP アドレスを選択します。
2. **[DNS 名ラベル]** の下に、使用するプレフィックスを入力します。
3. ページの最上部で **[保存]** を選択します。
4. 左側のメニューの **[概要]** を選択して、VM の概要ブレードに戻ります。
5. **[DNS 名]** が正しく表示されていることを確認します。 

## <a name="next-steps"></a>次のステップ

[Azure DNS ゾーン](../dns/dns-getstarted-portal.md)を使用して DNS を管理することもできます。

