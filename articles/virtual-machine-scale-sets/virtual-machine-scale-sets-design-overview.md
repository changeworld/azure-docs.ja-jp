<properties
	pageTitle="VM スケール セットの規模を設計する | Microsoft Azure"
	description="VM スケール セットの規模を設計する方法について説明します"
	keywords="Linux 仮想マシン,仮想マシン スケール セット" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="gatneil"/>

# VM スケール セットの規模を設計する

このトピックでは、仮想マシン スケール セットの設計に関する考慮事項について説明します。仮想マシン スケール セットに関する情報については、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。


## Storage

スケール セットはストレージ アカウントを利用し、セットに VM の OS ディスクを保管します。ストレージ アカウントあたり 20 以下の VM という比率が推奨されます。また、ストレージ アカウント名の始めの文字にアルファベットを使用することが推奨されます。異なる内部システムの負荷を分散するのに役立ちます。たとえば、次のテンプレートでは、uniqueString ARM テンプレート機能を利用し、ストレージ アカウント名の先頭に追加するプレフィックス ハッシュを生成します。[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)


## オーバープロビジョニング

2016-03-30 API バージョンより、VM スケール セットは VM を "オーバープロビジョニング" するように初期設定されます。つまり、スケール セットはユーザーが要求した数より多い VM を実際に起動し、それから不要な VM を削除します。この方法でプロビジョニングの成功率が上がります。1 つの VM のプロビジョニングが成功しないだけで、Azure リソース マネージャーはデプロイ全体を "失敗" と見なすためです。必要以上の VM については請求されません。割り当て上限でもカウントされません。

プロビジョニングの成功率を上げますが、通知なしで消える VM を処理するように設計されていないアプリケーションにとって、混乱を招く動作を引き起こすことがあります。オーバープロビジョニングをオフにするには、テンプレートに "overprovision": false という文字列を与えます。

オーバープロビジョニングをオフにすると、ストレージ アカウントあたりの VM 数を上げることができますが、40 を超えることは推奨されません。


## 上限 (プラットフォームとカスタム)
プラットフォーム イメージで構築されたスケール セットは 100 VM に制限されます。このスケールには、5 ストレージ アカウントが推奨されます。ただし、カスタム イメージで構築されたスケール セット (自分で構築したスケール セット) の場合、1 つのストレージ アカウントですべての OS ディスク vhd を作成する必要があります。そのため、カスタム イメージで構築されたスケール セットの VM の最大数は 20 です。オーバープロビジョニングをオフにすると、最大 40 になります。


## 大規模

スケール セットの上限は、プラットフォーム イメージで 100 VM に、カスタム イメージで 20 VM になります (オーバープロビジョニングなしで 40)。それ以上が必要な場合、複数のスケール セットをデプロイする必要があります。その方法の例が必要な場合、このテンプレート (https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale) を参照してください。

<!---HONumber=AcomDC_0427_2016-->