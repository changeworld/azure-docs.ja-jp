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
ms.openlocfilehash: e583808b0be0ff4105abc438ace1b52b9d3317eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726507"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows の入れ子になった仮想化
Azure IoT Edge for Linux on Windows と互換性のある入れ子になった仮想化は 2 つの形式があります。 ユーザーは、ローカル VM または Azure VM からデプロイすることを選択できます。 この記事では、ユーザーのシナリオに最適なオプションを明確にし、構成要件について分析情報を提供します。

> [!NOTE]
>
> 入れ子になった仮想化に対して[ネットワーク オプション](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options)を確実に 1 つ有効にします。 そうしないと、EFLOW インストール エラーが発生します。 

## <a name="deployment-on-local-vm"></a>ローカル VM でのデプロイ

これは、Azure IoT Edge for Linux on Windows をホストする Windows VM のベースライン アプローチです。 この場合、デプロイを開始する前に、入れ子になった仮想化を有効にする必要があります。 このシナリオを構成する方法の詳細については、[入れ子になった仮想化による仮想マシンでの Hyper-V の実行](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)に関する記事をご覧ください。

Windows Server を使用している場合は、[Hyper-V ロールがインストール](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)されていることをご確認ください。

## <a name="deployment-on-azure-vms"></a>Azure VM でのデプロイ

Azure IoT Edge for Linux on Windows は、既定のスイッチを起動するスクリプトが実行されない限り、サーバー SKU を実行している Azure VM と互換性がありません。 既定のスイッチを起動する方法の詳細については、[Windows での Linux 用仮想スイッチの作成](how-to-create-virtual-switch.md)に関するページを参照してください。

> [!NOTE]
>
> EFLOW をホストすることになる Azure VM はすべて、[入れ子になった仮想化をサポート](../virtual-machines/acu.md)する VM である必要があります
