---
title: Azure VMware Solution - AVS プライベート クラウド用のワークロード DNS と DHCP を設定する
description: AVS プライベート クラウド環境で実行されるアプリケーションとワークロードのために、DNS および DHCP を設定する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024689"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>AVS プライベート クラウドで DNS および DHCP アプリケーションとワークロードを設定する

AVS プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。 これらのサービスを提供する、適切な DHCP と DNS インフラストラクチャが必要です。 AVS プライベート クラウド環境でこれらのサービスを提供するように、仮想マシンを構成することができます。 

## <a name="prerequisites"></a>前提条件

* VLAN が構成された分散ポート グループ
* オンプレミスまたはインターネット ベースの DNS サーバーへのルート設定
* 仮想マシンを作成するための仮想マシン テンプレートまたは ISO

## <a name="linux-based-dns-server-setup"></a>Linux ベースの DNS サーバーの設定

Linux では、DNS サーバーを設定するためのさまざまなパッケージが提供されています。 ここでは、[DigitalOcean による設定例](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)と、オープンソースの BIND DNS サーバーを設定する手順を示します。

## <a name="windows-based-setup"></a>Windows ベースのセットアップ

これらの Microsoft のトピックでは、Windows サーバーを DNS サーバーおよび DHCP サーバーとして設定する方法について説明しています。

* [DNS サーバーとしての Windows Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [DHCP サーバーとしての Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
