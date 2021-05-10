---
title: Azure IoT Edge for Linux on Windows の入れ子になった仮想化 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows で入れ子になった仮想化を操作する方法について説明します。
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608752"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows の入れ子になった仮想化
Azure IoT Edge for Linux on Windows と互換性のある入れ子になった仮想化は 2 つの形式があります。 ユーザーは、ローカル VM または Azure VM からデプロイすることを選択できます。 この記事では、ユーザーのシナリオに最適なオプションを明確にし、構成要件について分析情報を提供します。

> [!NOTE]
>
> 入れ子になった仮想化に対して[ネットワーク オプション](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) を確実に 1 つ有効にします。 そうしないと、EFLOW インストール エラーが発生します。 

## <a name="deployment-on-local-vm"></a>ローカル VM でのデプロイ
これは、Azure IoT Edge for Linux on Windows をホストする Windows VM のベースライン アプローチです。 この場合、デプロイを開始する前に、入れ子になった仮想化を有効にする必要があります。 このシナリオを構成する方法の詳細については、[入れ子になった仮想化による仮想マシンでの Hyper-V の実行](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)に関する記事をご覧ください。

Windows Server を使用している場合は、[Hyper-V ロールがインストール](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)されていることをご確認ください。

## <a name="deployment-on-azure-vms"></a>Azure VM でのデプロイ
Azure VM でデプロイすることを選択した場合、設計上、外部スイッチがないことにご注意ください。 Azure IoT Edge for Linux on Windows は、既定のスイッチを起動する特定のスクリプトが実行されない限り、サーバー SKU を実行している Azure VM でも互換性がありません。 既定のスイッチを起動する方法の詳細については、後述の「[Windows Server](#windows-server)」セクションを参照してください。 

> [!NOTE]
>
> EFLOW をホストすることになる Azure VM はすべて、[入れ子になった仮想化をサポート](../virtual-machines/acu.md)する VM である必要があります


## <a name="limited-use-of-external-switch"></a>外部スイッチの限定的な使用
VM が外部スイッチを介して IP アドレスを取得できないシナリオでは、デプロイ機能によって、デプロイ用に内部の既定のスイッチが自動的に使用されます。 これは、Azure IoT Edge for Linux VM の管理はターゲット デバイス自体でのみ実行できることを意味します (つまり、WAC PowerShell SSH 拡張機能を使用して Azure IoT Edge for Linux VM に接続することは、localhost でのみ可能です)。

## <a name="windows-server"></a>Windows Server
Windows Server ユーザーの場合、Azure IoT Edge for Linux on Windows により、既定のスイッチが自動的にはサポートされないことにご注意ください。

* ローカル VM の結果: EFLOW VM が外部スイッチを介して IP アドレスを取得できることを確保します。

* Azure VM の結果: Azure VM に外部スイッチがないため、サーバーで内部スイッチを設定する前に EFLOW をデプロイすることはできません。

既定では、サーバー SKU には既定のスイッチはありません (サーバー SKU が Azure VM 上で実行されているかどうかにかかわらず)。 外部スイッチを使用できない Azure VM でデプロイする場合は、Azure IoT Edge for Linux on Windows デプロイの前に、既定のスイッチを手動で設定して構成する必要があります。 内部スイッチの IP 構成、NAT 構成、DHCP サーバーのインストールと構成が必要であり、デプロイ機能はこれに対応しています。 パブリック プレビュー段階のデプロイ機能は、これらの設定が変更されることで生産的なデプロイのネットワーク構成が損なわれることがないように設定されています。

* 既定のスイッチを手動で設定する方法については、[Azure Virtual Machines で入れ子になった仮想化を有効にする方法](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)に関する記事をご覧ください
* このシナリオ用に DHCP サーバーを設定する方法については、[Windows PowerShell を使用した DHCP のデプロイ](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)に関するドキュメントをご覧ください
