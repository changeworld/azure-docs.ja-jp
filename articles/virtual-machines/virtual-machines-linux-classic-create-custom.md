<properties
	pageTitle="Linux VM の作成 | Microsoft Azure"
	description="Linux オペレーティング システムを実行するクラシック デプロイ モデルを使用してカスタム仮想マシンを作成する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="iainfou"/>

# カスタム Linux VM を作成する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

このトピックでは、クラシック デプロイメント モデルを使用して Azure CLI でカスタム仮想マシン (VM) を作成する方法について説明します。ここでは、Azure 上の使用可能な**イメージ**にある Linux イメージを使用します。Azure CLI コマンドでは、次のような構成を選択できます。

- 仮想ネットワークと VM との接続
- 既存のクラウド サービスへの VM の追加
- 既存のストレージ アカウントへの VM の追加
- 可用性セットまたは場所への VM の追加

> [AZURE.IMPORTANT] 仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるには、仮想マシンの作成時に必ず仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[Virtual Network の概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。


## クラシック デプロイ モデルを使用して Linux 仮想マシンを作成する方法

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=AcomDC_0824_2016-->