---
title: 仮想マシンの概要
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 仮想マシンとその利点について学習します。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372791"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 仮想マシンの概要

CloudSimple では、Azure portal から VMware 仮想マシン (VM) を管理できます。  クラスターまたは vSphere クラスターのリソース プールは、サブスクリプションにマップすることによって Azure を介して管理されます。

Azure から CloudSimple VM を作成するには、VM テンプレートがお使いのプライベート クラウド vCenter に存在している必要があります。  テンプレートは、オペレーティング システムとアプリケーションをカスタマイズするために使用されます。  テンプレート VM は、企業のセキュリティ ポリシーを満たすように強化することができます。  テンプレートを使用して VM を作成し、セルフサービス モデルを使用して Azure portal から VM を使用することができます。

## <a name="benefits"></a>メリット

Azure portal から利用できる CloudSimple 仮想マシンは、ユーザーが VMware 仮想マシンを作成および管理するためのセルフサービス メカニズムを提供します。

* プライベート クラウド vCenter に CloudSimple VM を作成する
* VM プロパティを管理する
  * ディスクを追加/削除する
  * NIC を追加/削除する
* CloudSimple VM の電源操作
  * 電源オンと電源オフ
  * VM をリセットする
* VM を削除する

## <a name="next-steps"></a>次のステップ

* [Azure で VMware VM を使用する](quickstart-create-vmware-virtual-machine.md)方法を学習する
* [Azure サブスクリプションをマップする](azure-subscription-mapping.md)方法を学習する
