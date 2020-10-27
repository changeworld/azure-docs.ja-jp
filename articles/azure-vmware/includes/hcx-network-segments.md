---
title: VMware HCX のネットワーク セグメント
description: VMware HCX に必要な 4 つのネットワークがあります。
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173639"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX には、次の 4 つのネットワークが必要です。

- **管理ネットワーク:** 通常は、vSphere クラスターでも使用される管理ネットワークです。 VMware HCX 用のこのネットワーク セグメントで少なくとも 2 つの IP を識別します。 (デプロイによっては、より多くのものが必要になる場合があります)。

- **vMotion ネットワーク:** 通常は、vSphere クラスターの vMotion にも使用されるネットワークです。  VMware HCX 用のこのネットワーク セグメントで少なくとも 2 つの IP を識別します。 (デプロイによっては、より多くのものが必要になる場合があります)。  

   vMotion ネットワークは、分散仮想スイッチまたは vSwitch0 で公開する必要があります。 そうでない場合は環境を変更します。

   > [!NOTE]
   > このネットワークがルーティングされない場合 (プライベートな場合) は問題ありません。

- **アップリンク ネットワーク:** VMware HCX アップリンク用の新しいネットワークを作成し、ポート グループを使用して vSphere クラスターに拡張します。 VMware HCX 用のこのネットワーク セグメントで少なくとも 2 つの IP を識別します。 (デプロイによっては、より多くのものが必要になる場合があります)。  

   > [!NOTE]
   > 推奨される方法は /29 ネットワークを作成することですが、どのネットワーク サイズでもかまいません。

- **レプリケーション ネットワーク:** VMware HCX レプリケーション用の新しいネットワークを作成し、ポート グループを使用してそのネットワークを vSphere クラスターに拡張します。 VMware HCX 用のこのネットワーク セグメントで少なくとも 2 つの IP を識別します。 (デプロイによっては、より多くのものが必要になる場合があります)。

   > [!NOTE]
   > 推奨される方法は /29 ネットワークを作成することですが、どのネットワーク サイズでもかまいません。