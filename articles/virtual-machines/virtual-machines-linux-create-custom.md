<properties
	pageTitle="Linux VM の作成 | Microsoft Azure"
	description="Linux オペレーティング システムを実行するクラシック デプロイ モデルを使用してカスタム仮想マシンを作成する方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Azure 上で Linux を実行するカスタム仮想マシンを作成する方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルを使用したリソースの作成について説明します。また、[リソース マネージャー デプロイ モデル](virtual-machines-linux-tutorial.md)を使用してリソースを作成することもできます。

このトピックでは、Azure CLI でクラシック デプロイ モデルを使用して*カスタム*仮想マシンを作成する方法について説明します。Azure の**イメージ**にある Linux イメージを使用します。Azure CLI では、次のような構成を選択できます。

- 仮想ネットワークと VM との接続
- 既存のクラウド サービスへの VM の追加
- 既存のストレージ アカウントへの VM の追加
- 可用性セットまたは場所への VM の追加

> [AZURE.IMPORTANT]仮想マシンが仮想ネットワークを使用して、ホスト名を使用した直接接続またはクロスプレミス接続の設定ができるには、仮想マシンの作成時に必ず仮想ネットワークを指定する必要があります。仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。仮想ネットワークの詳細については、「[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。

<p/>


- [Linux を実行する仮想マシンの作成](virtual-machines-linux-tutorial.md)


## クラシック デプロイ モデルを使用して Linux 仮想マシンを作成する方法

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=Oct15_HO1-->