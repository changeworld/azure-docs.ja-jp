<properties
   pageTitle="Azure ポータルでの VM の FQDN の作成 | Microsoft Azure"
   description="Azure ポータルで仮想マシンに基づいて、リソース マネージャーの完全修飾ドメイン名 (FQDN) を作成する方法を説明します。"
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Azure ポータルでの完全修飾ドメイン名の作成

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。


**リソース マネージャー** デプロイ モデルを使用して [Azure ポータル](https://portal.azure.com) で仮想マシンを作成すると、ポータルによって仮想マシン用のパブリック IP リソースが作成されます。この IP アドレスを使用して、仮想マシンにリモートでアクセスできます。ポータルでは、既定では[完全修飾ドメイン名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) が作成されませんが、仮想マシンの作成後に非常に簡単に作成できます。この記事では、DNS 名または FQDN を作成する手順を示します。

この記事では、ポータルでサブスクリプションにログインし、**リソース マネージャー** を使用して、使用可能なイメージで仮想マシンを作成していることを前提としています。仮想マシンの起動後、次の手順に従います。

1.  ポータルで仮想マシンの設定を表示し、パブリック IP アドレスをクリックします。

    ![IP リソースの検索](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  パブリック IP の DNS 名が空白であることに注意してください。パブリック IP ブレードの **[すべての設定]** をクリックします。

    ![settings ip](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  パブリック IP の設定の **[構成]** タブを開きます。目的の DNS 名ラベルを入力し、この構成を**保存**します。

    ![DNS 名のラベルの入力](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    これで、パブリック IP リソースがブレードでこの新しい DNS ラベルを表示するようになります。

4.  パブリック IP ブレードを閉じ、ポータルの仮想マシン ブレードに戻ります。パブリック IP リソースの IP アドレスの横に DNS 名/FQDN が表示されることを確認します。

    ![FQDN が作成される](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    この DNS 名を使用して、仮想マシンにリモートで接続できるようになります。たとえば、`SSH adminuser@testdnslabel.centralus.cloudapp.azure.com` を使用して、完全修飾ドメイン名が `testdnslabel.centralus.cloudapp.azure.com` でユーザー名が `adminuser` の Linux 仮想マシンに接続できます。

<!---HONumber=AcomDC_0128_2016-->