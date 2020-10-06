---
title: VMware HCX のネットワーク セグメント
description: VMware HCX に必要な 4 つのネットワークがあります。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578872"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX に必要な 4 つのネットワークは次のとおりです。

- **管理ネットワーク:** 通常は、vSphere クラスターでも使用される管理ネットワークです。  少なくとも、VMware HCX のこのネットワーク セグメントで 2 つの IP を特定します (展開によっては、さらに多くの IP が必要になる場合があります)。

- **vMotion ネットワーク:** 通常は、vSphere クラスターの vMotion にも使用されるネットワークです。  少なくとも、VMware HCX のこのネットワーク セグメントで 2 つの IP を特定します (展開によっては、さらに多くの IP が必要になる場合があります)。  

   vMotion ネットワークは、分散仮想スイッチまたは vSwitch0 で公開する必要があります。 そうでない場合は環境を変更します。

   > [!NOTE]
   > このネットワークがルーティングされない (プライベート) 場合は問題ありません。

- **アップリンク ネットワーク:** VMware HCX アップリンク用の新しいネットワークを作成し、ポート グループを使用して vSphere クラスターに拡張します。  少なくとも、VMware HCX のこのネットワーク セグメントで 2 つの IP を特定します (展開によっては、さらに多くの IP が必要になる場合があります)。  

   > [!NOTE]
   > 推奨される方法は /29 ネットワークを作成することですが、どのネットワーク サイズでもかまいません。

- **レプリケーション ネットワーク:** VMware HCX レプリケーション用の新しいネットワークを作成し、ポート グループを使用してそのネットワークを vSphere クラスターに拡張します。  少なくとも、VMware HCX のこのネットワーク セグメントで 2 つの IP を特定します (展開によっては、さらに多くの IP が必要になる場合があります)。

   > [!NOTE]
   > 推奨される方法は /29 ネットワークを作成することですが、どのネットワーク サイズでもかまいません。