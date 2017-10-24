---
title: "Azure ポータルを使用した仮想マシン スケール セットの作成 | Microsoft Docs"
description: "Azure Portal を使用してスケール セットをデプロイします。"
keywords: "仮想マシン スケール セット"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Azure Portal を使用して Virtual Machine Scale Set を作成する方法
このチュートリアルでは、Azure Portal を使用して仮想マシン スケール セットをわずか数分で簡単に作成する方法を示します。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="choose-the-vm-image-from-the-marketplace"></a>Marketplace から VM イメージを選択する
ポータルから、CentOS、CoreOS、Debian、Ubuntu Server、その他の Linux イメージ、Windows Server イメージを使用して、スケール セットを簡単にデプロイできます。

まず、Web ブラウザーで、 [Azure Portal](https://portal.azure.com) に移動します。 **[新規]** をクリックし、**[スケール セット]** を検索して、**[仮想マシン スケール セット]** エントリを選択します。

![Azure 仮想マシン スケール セット ポータル検索](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>スケール セットを作成する
既定の設定を使って、スケール セットをすぐに作成できます。

* スケール セットの名前を入力します。  
この名前は、スケール セットの前に設置されるロード バランサーの FQDN のベースになります。そのため、Azure 全体で一意の名前にする必要があります。

* 目的の OS の種類を選択します。

* 目的のユーザー名を入力して、任意の認証の種類を選択します。  
パスワードを選択する場合、パスワードは 12 文字以上にする必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。 [ユーザー名とパスワードの要件](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページで詳細を確認してください。 **[SSH 公開キー]** を選択した場合は、秘密キー "*ではなく*"、公開キーのみに貼り付けます。

* **[インスタンス数が 100 を超えるスケールを有効にする]** に対して **[はい]** または **[いいえ]** を選択します。  
[はい] の場合、スケール セットは複数の配置グループにまたがることができます。 詳しくは、[こちらのドキュメント](./virtual-machine-scale-sets-placement-groups.md)をご覧ください。

* 必ず適切な**インスタンス サイズ**を選択してください。  
仮想マシンのサイズの詳細については、[Windows VM のサイズ](..\virtual-machines\windows\sizes.md)または [Linux VM のサイズ](..\virtual-machines\linux\sizes.md)に関する記事をご覧ください。

* 目的のリソース グループ名と場所を入力します。  
リージョンと**インスタンス サイズ**で可用性ゾーンがサポートされる場合は、**[Availability zones]\(可用性ゾーン\)** フィールドが有効になります。 可用性ゾーンの詳細については、こちらの[概要](../availability-zones/az-overview.md)記事をご覧ください。

* 目的のドメイン名のラベル (スケール セットの前に設置されたロード バランサーの FQDN のベース) を入力します。  
このラベルは、Azure 全体で一意の名前にする必要があります。

* 目的のオペレーティング システム ディスク イメージ、インスタンス数、マシンのサイズを選択します。

* 目的のディスクの種類を、管理または非管理対象から選びます。  
詳しくは、[こちらのドキュメント](./virtual-machine-scale-sets-managed-disks.md)をご覧ください。 複数の配置グループにまたがるスケール セットを指定した場合は、スケール セットが複数の配置グループにまたがるには管理ディスクが必要であるため、このオプションは変更できません。

* 自動スケールを有効または無効にし、有効にした場合は次のように構成します。

![Azure 仮想マシン スケール セット ポータル作成のプロンプト](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>スケール セットの VM に接続する
スケール セットを 1 つの配置グループに制限することを選んだ場合は、スケール セットに簡単に接続できるように構成された NAT 規則と共に、スケール セットがデプロイされます (この規則がない場合は、スケール セットの仮想マシンに接続するには、スケール セットと同じ仮想ネットワークにジャンプボックス作成する必要があります)。 これらの NAT 規則を表示するには、スケール セットのロード バランサーの [`Inbound NAT Rules`] タブに移動します。

![Azure 仮想マシン スケール セット ポータルの NAT 規則](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

これらの NAT 規則を使用して、スケール セット内の各 VM に接続することができます。 たとえば、Windows スケール セットで、受信ポート 50000 の NAT 規則がある場合、 `<load-balancer-ip-address>:50000`の RDP を経由してマシンに接続することができます。 Linux スケール セットの場合、 `ssh -p 50000 <username>@<load-balancer-ip-address>`コマンドを使用して接続することができます。

## <a name="next-steps"></a>次のステップ
CLI を使用してスケール セットをデプロイする方法については、 [このドキュメント](virtual-machine-scale-sets-cli-quick-create.md)をご覧ください。

PowerShell を使用してスケール セットをデプロイする方法については、 [このドキュメント](virtual-machine-scale-sets-windows-create.md)をご覧ください。

Visual Studio を使用してスケール セットをデプロイする方法については、 [このドキュメント](virtual-machine-scale-sets-vs-create.md)をご覧ください。

全般的なドキュメントについては、 [スケール セットの概要に関するページ](virtual-machine-scale-sets-overview.md)をご覧ください。

全般的な情報については、 [スケール セットのメイン ランディング ページ](https://azure.microsoft.com/services/virtual-machine-scale-sets/)をご覧ください。

