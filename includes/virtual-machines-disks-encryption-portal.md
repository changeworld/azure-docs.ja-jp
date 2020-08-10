---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177037"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM をデプロイする

キー コンテナーとディスク暗号化セットの作成と設定が完了したので、暗号化を使用して VM をデプロイできます。
VM のデプロイ プロセスは標準的なデプロイ プロセスと似ています。唯一の違いは、VM を他のリソースと同じリージョンにデプロイしたうえで、カスタマー マネージド キーを使用する必要があることです。

1. 「**Virtual Machines**」で検索し、 **[+ 追加]** を選択して、VM を作成します。
1. **[基本]** ブレードで、ディスク暗号化セットおよび Azure Key Vault と同じリージョンを選択します。
1. **[基本]** ブレードで、必要に応じてその他の値を入力します。

    ![リージョンの値が強調表示されている、VM 作成エクスペリエンスのスクリーンショット。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. **[ディスク]** ブレードで、 **[顧客が管理するキーを使用した保存時の暗号化]** を選択します。
1. **[ディスク暗号化セット]** ボックスの一覧で、使用するディスク暗号化セットを選択します。
1. 必要に応じて、残りの選択を行います。

    ![VM 作成エクスペリエンスの [ディスク] ブレードのスクリーンショット。 [ディスク暗号化セット] ドロップダウンが強調表示された状態です。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>既存のディスクで有効にする

> [!CAUTION]
> VM にアタッチされているすべてのディスクでディスク暗号化を有効にするには、VM を停止する必要があります。
    
1. 使用しているディスク暗号化セットのいずれかと同じリージョンにある VM に移動します。
1. VM を開き、 **[停止]** を選択します。

    ![[停止] ボタンが強調表示された、サンプル VM のメイン オーバーレイのスクリーンショット。](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM の停止が完了した後に、 **[ディスク]** を選択し、暗号化するディスクを選択します。

    ![[ディスク] ブレードが開いた状態のサンプル VM のスクリーンショット。 選択するディスクの例として、OS ディスクが強調表示されています。](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. **[暗号化]** を選択し、 **[顧客が管理するキーを使用した保存時の暗号化]** を選択してから、ボックスの一覧で、ディスク暗号化セットを選択します。
1. **[保存]** を選択します。

    ![サンプル OS ディスクのスクリーンショット。 [暗号化] ブレードが開いており、[顧客が管理するキーを使用した保存時の暗号化] が選択され、Azure Key Vault の例が表示されている。 これらの選択を行った後、[保存] ボタンを選択します。](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 暗号化する VM にアタッチされている他のすべてのディスクに対して、このプロセスを繰り返します。
1. 使用するディスクでカスタマー マネージド キーへの切り替えが完了し、暗号化するアタッチ済みディスクが他になくなったら、VM を起動することができます。
