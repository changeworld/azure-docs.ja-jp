---
title: Azure VMware Solutions (AVS) - 仮想マシンの概要
description: AVS 仮想マシンとその利点について説明します。
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024910"
---
# <a name="avs-virtual-machines-overview"></a>AVS 仮想マシンの概要

AVS では、Azure portal から VMware 仮想マシン (VM) を管理できます。 クラスターまたは vSphere クラスターのリソース プールは、サブスクリプションにマップすることによって Azure を介して管理されます。

Azure から AVS VM を作成するには、VM テンプレートがお使いの AVS プライベート クラウド vCenter に存在している必要があります。 テンプレートは、オペレーティング システムとアプリケーションをカスタマイズするために使用されます。 テンプレート VM は、企業のセキュリティ ポリシーを満たすように強化することができます。 テンプレートを使用して VM を作成し、セルフサービス モデルを使用して Azure portal から VM を使用することができます。

## <a name="benefits"></a>メリット

Azure portal から利用できる AVS 仮想マシンは、ユーザーが VMware 仮想マシンを作成および管理するためのセルフサービス メカニズムを提供します。

* AVS プライベート クラウド vCenter に AVS VM を作成する
* VM プロパティを管理する
  * ディスクを追加/削除する
  * NIC を追加/削除する
* AVS VM の電源操作
  * 電源オンと電源オフ
  * VM をリセットする
* VM を削除する

## <a name="next-steps"></a>次のステップ

* [Azure で VMware VM を使用する](quickstart-create-vmware-virtual-machine.md)方法を学習する
* [Azure サブスクリプションをマップする](azure-subscription-mapping.md)方法を学習する
