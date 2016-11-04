---
title: VM スケール セットの規模を設計する | Microsoft Docs
description: VM スケール セットの規模を設計する方法について説明します
keywords: Linux 仮想マシン,仮想マシン スケール セット
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: gatneil

---
# VM スケール セットの規模を設計する
このトピックでは、仮想マシン スケール セットの設計に関する考慮事項について説明します。仮想マシン スケール セットに関する情報については、「[仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。

## Storage
スケール セットはストレージ アカウントを利用し、セットに VM の OS ディスクを保管します。ストレージ アカウントあたり 20 以下の VM という比率が推奨されます。また、ストレージ アカウント名の始めの文字にアルファベットを使用することが推奨されます。これは、異なる内部システムの負荷を分散するのに役立ちます。たとえば、次のテンプレートでは、uniqueString Resource Manager テンプレート関数を利用し、ストレージ アカウント名の先頭に追加するプレフィックス ハッシュを生成しています。[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

## オーバープロビジョニング
"2016-03-30" API バージョンより、VM スケール セットは VM を "オーバープロビジョニング" するように初期設定されています。オーバープロビジョニングがオンに設定されていると、スケール セットは要求された数よりも多くの VM を起動し、最後に起動した余分な仮想マシンを削除します。オーバープロビジョニングによって、プロビジョニングの成功率がアップします。必要以上の VM については請求されません。割り当て制限でもカウントされません。

オーバープロビジョニングはプロビジョニングの成功率を上げますが、通知なしで消える VM を処理するように設計されていないアプリケーションにとって、混乱を招く動作を引き起こすことがあります。オーバープロビジョニングをオフにするには、テンプレートに "overprovision": "false" という文字列を与えます。詳細については、[VM スケール セット REST API ドキュメント](https://msdn.microsoft.com/library/azure/mt589035.aspx)をご覧ください。

オーバープロビジョニングをオフにすると、ストレージ アカウントあたりの VM 数を上げることができますが、40 を超えることは推奨されません。

## 制限
カスタム イメージで構築されたスケール セット (自分で構築したスケール セット) の場合、1 つのストレージ アカウント内にすべての OS ディスク VHD を作成する必要があります。その結果、カスタム イメージで構築されたスケール セットの VM の推奨される最大数は 20 になります。オーバープロビジョニングをオフにすると、最大 40 になります。

プラットフォーム イメージで構築されたスケール セットは現在 100 VM に制限されています (このスケールには、5 ストレージ アカウントが推奨されます)。

VM の数が、これらの制限で許可されている数を超える場合は、[こちらのテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)に示すように、複数のスケール セットをデプロイする必要があります。

<!---HONumber=AcomDC_0817_2016-->