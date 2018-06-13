---
title: Azure Portal での仮想マシン スケール セットの作成 | Microsoft Docs
description: Azure Portal で仮想マシン スケール セットをすばやく作成する方法を説明します
keywords: 仮想マシン スケール セット
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6157f15ef471676424a2f7027c7e9299e2218d4b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201323"
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Azure Portal での仮想マシン スケール セットの作成
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 この入門記事では、Azure Portal で仮想マシン スケール セットを作成します。 スケール セットは、[Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) または [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) を使用して作成することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal (http://portal.azure.com) にログインします。


## <a name="create-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する
Windows Server イメージまたは Linux イメージ (RHEL、CentOS、Ubuntu、SLES など) を含むスケール セットをデプロイできます。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. "*スケール セット*" を検索し、**[仮想マシン スケール セット]** を選択し、**[作成]** を選択します。
3. スケール セットの名前を入力します (*myScaleSet* など)。
4. 目的の OS の種類を選択します (*Windows Server 2016 Datacenter* など)。
5. 目的のリソース グループ名 (*myResourceGroup* など) と場所 ("*米国東部*" など) を入力します。
6. 目的のユーザー名を入力して、任意の認証の種類を選択します。
    - **パスワード**は、12 文字以上で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 つの要件のうち、3 つを満たしている必要があります。 詳細については、[ユーザー名とパスワードの要件](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)を参照してください。
    - Linux OS ディスク イメージを選択した場合は、代わりに **[SSH public key]\(SSH 公開キー\)** を選択できます。 公開キーのみを指定してください (*~/.ssh/id_rsa.pub* など)。 ポータルから Azure Cloud Shell を使用して、[SSH キー](../virtual-machines/linux/mac-create-ssh-keys.md)を作成および使用することができます。

7. **パブリック IP アドレス名**を入力します (*myPublicIP* など)。
8. 一意の**ドメイン名ラベル**を入力します (*myuniquedns* など)。 この DNS ラベルが、スケール セットの前のロード バランサーの FQDN のベースになります。
9. スケール セットのオプションを確認するために、**[作成]** を選択します。

    ![Azure Portal での仮想マシン スケール セットの作成](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>スケール セットの VM に接続する
ポータルでスケール セットを作成すると、ロード バランサーが作成されます。 ネットワーク アドレス変換 (NAT) 規則は、RDP や SSH などのリモート接続用のスケール セット インスタンスにトラフィックを分散するために使用されます。

スケール セット インスタンスに対するこれらの NAT 規則と接続情報を表示するには、次の操作を行います。

1. 前の手順で作成したリソース グループ (*myResourceGroup* など) を選択します。
2. リソースの一覧から**ロード バランサー** (*myScaleSetLab* など) を選択します。
3. ウィンドウの左側のメニューから **[受信 NAT 規則]** を選択します。

    ![受信 NAT 規則を使用して、仮想マシン スケール セットのインスタンスに接続できます](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

これらの NAT 規則を使用して、スケール セット内の各 VM に接続することができます。 それぞれの VM インスタンスの宛先 IP アドレスと TCP ポートの値が表示されます。 たとえば、宛先 IP アドレスが *104.42.1.19* で、TCP ポートが *50001* の場合は、次のように VM インスタンスに接続します。

- Windows スケール セットの場合は、`104.42.1.19:50001` で、RDP を使用して VM インスタンスに接続します。
- Linux スケール セットの場合は、`ssh azureuser@104.42.1.19 -p 50001` で、SSH を使用して VM インスタンスに接続します。

プロンプトが表示されたら、前の手順でスケール セットを作成したときに指定した資格情報を入力します。 スケール セット インスタンスは、通常の VM であり、通常どおりに対話できます。 スケール セット インスタンスにアプリケーションをデプロイして実行する方法の詳細については、「[仮想マシン スケール セットへのアプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)」を参照してください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、リソース グループ、スケール セット、およびすべての関連リソースを削除します。 そのためには、VM のリソース グループを選択し、**[削除]** をクリックします。


## <a name="next-steps"></a>次の手順
この入門記事では、Azure Portal で基本的な仮想マシン スケール セットを作成しました。 スケーラビリティとオートメーションを高めるために、次の操作方法に関する記事を参照してスケール セットを拡張してください。

- [仮想マシン スケール セットへのアプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)[Azure CLI](virtual-machine-scale-sets-autoscale-cli.md)、または [Azure ポータル](virtual-machine-scale-sets-autoscale-portal.md)を使用した自動的なスケーリング
- [スケール セット VM インスタンスに対する OS の自動アップグレードの使用](virtual-machine-scale-sets-automatic-upgrade.md)
