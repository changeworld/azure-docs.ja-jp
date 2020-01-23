---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 662b2792a2e09603425b1988138326799334f323
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973433"
---
### <a name="portal"></a>ポータル

ディスクにカスタマー マネージド キーを設定する作業を初めて実行するときは、特定の順序でリソースを作成する必要があります。 最初に、Azure キー コンテナーの作成と設定を行う必要があります。

#### <a name="setting-up-your-azure-key-vault"></a>Azure Key Vault の設定

1. [Azure portal](https://portal.azure.com/) にサインインして、Key Vault を検索します。
1. **[キー コンテナー]** を検索して選択します。

    [ ![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > デプロイを成功させるには、Azure キー コンテナー、ディスク暗号化セット、VM、ディスク、スナップショットがすべて同じリージョンとサブスクリプションに存在している必要があります。

1. **[+ 追加]** を選択して、新しいキー コンテナーを作成します。
1. 新しいリソース グループを作成する
1. キー コンテナー名を入力し、リージョンを選択して、価格レベルを選択します。
1. **[確認および作成]** を選択し、選択内容を確認してから、 **[作成]** を選択します。

    ![sse-create-a-key-vault.png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. キー コンテナーのデプロイが完了したら、それを選択します。
1. **[設定]** で **[キー]** を選択します。
1. **[生成/インポート]** を選択します。

    ![sse-key-vault-generate-settings.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. **[キーの種類]** は **[RSA]** 、 **[RSA キー サイズ]** は **[2080]** に設定されているので、どちらもそのままにしておきます。
1. 必要に応じて残りの選択項目を入力したら、 **[作成]** を選択します。

    ![sse-create-a-key-generate.png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>ディスク暗号化セットを設定する

ディスク暗号化セットを作成して構成するには、次のリンクを使用する必要があります: https://aka.ms/diskencryptionsets 。 ディスク暗号化セットの作成は、グローバル Azure portal ではまだ利用できません。

1. [ディスク暗号化セットのリンク](https://aka.ms/diskencryptionsets)を開きます。
1. **[+追加]** を選択します。

    ![sse-create-disk-encryption-set.png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. リソース グループを選択し、暗号化セットに名前を付け、キー コンテナーと同じリージョンを選択します。
1. **[キー コンテナーとキー]** を選択します。
1. 以前に作成したキー コンテナーとキーのほか、バージョンを選択します。
1. **[選択]** を選択します。
1. **[確認および作成]** 、 **[作成]** の順に選択します。

    ![sse-disk-enc-set-blade-key.png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 作成が完了したら、ディスク暗号化セットを開き、ポップアップ表示されるアラートを選択します。

    ![sse-disk-enc-alert-fix.png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

2 つの通知がポップアップ表示され、成功します。 これで、キー コンテナーでディスク暗号化セットを使用できるようになります。

![disk-enc-notification-success.png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>VM をデプロイする

キー コンテナーとディスク暗号化セットの作成と設定が完了したので、暗号化を使用して VM をデプロイできます。
VM のデプロイ プロセスは標準的なデプロイ プロセスと似ています。唯一の違いは、VM を他のリソースと同じリージョンにデプロイしたうえで、カスタマー マネージド キーを使用する必要があることです。

1. 「**Virtual Machines**」で検索し、 **[+ 追加]** を選択して、VM を作成します。
1. **[基本]** タブで、ディスク暗号化セットおよび Azure Key Vault と同じリージョンを選択します。
1. **[基本]** タブで、必要に応じてその他の値を入力します。

    ![sse-create-a-vm-region.png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. **[ディスク]** タブで、 **[顧客が管理するキーを使用した保存時の暗号化]** を選択します。
1. **[ディスク暗号化セット]** ボックスの一覧で、使用するディスク暗号化セットを選択します。
1. 必要に応じて、残りの選択を行います。

    ![sse-create-vm-select-cmk-encryption-set.png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>既存のディスクで有効にする

既存のディスク上でディスク暗号化を管理および構成するには、次のリンクを使用する必要があります: https://aka.ms/diskencryptionsets 。 カスタマー マネージド キーを既存のディスク上で有効にする機能は、グローバル Azure portal ではまだ使用できません。

> [!CAUTION]
> VM にアタッチされているすべてのディスクでディスク暗号化を有効にするには、VM を停止する必要があります。

1. 使用しているディスク暗号化セットのいずれかと同じリージョンにある VM に移動します。
1. VM を開き、 **[停止]** を選択します。

    ![sse-stop-VM-to-encrypt-disk.png](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. VM の停止が完了した後に、 **[ディスク]** を選択し、暗号化するディスクを選択します。

    ![sse-existing-disk-select.png](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. **[暗号化]** を選択し、 **[顧客が管理するキーを使用した保存時の暗号化]** を選択してから、ボックスの一覧で、ディスク暗号化セットを選択します。
1. **[保存]** を選択します。

    ![sse-encrypt-existing-disk-customer-managed-key.png](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. 暗号化する VM にアタッチされている他のすべてのディスクに対して、このプロセスを繰り返します。
1. 使用するディスクでカスタマー マネージド キーへの切り替えが完了し、暗号化するアタッチ済みディスクが他になくなったら、VM を起動することができます。