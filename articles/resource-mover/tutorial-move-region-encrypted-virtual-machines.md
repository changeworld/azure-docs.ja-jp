---
title: Azure Resource Mover を使用して、リージョン間で暗号化された Azure VM を移動する
description: Azure Resource Mover を使用して、暗号化された Azure VM を別のリージョンに移動する方法について説明します。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600694"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>チュートリアル:暗号化された Azure VM をリージョン間で移動する

この記事では、[Azure Resource Mover](overview.md) を使用して、暗号化された Azure 仮想マシン (VM) を別の Azure リージョンに移動する方法について説明します。 

暗号化された VM とは、次のどちらかを言います。

- Azure Disk Encryption が有効になっているディスクが使用されている VM。 詳細については、[Azure portal を使用した Windows 仮想マシンの作成と暗号化](../virtual-machines/windows/disk-encryption-portal-quickstart.md)に関するページを参照してください。
- カスタマー マネージド キー (CMK) を使用して保存時の暗号化 (サーバー側暗号化) が適用されている VM。 詳細については、「[Azure portal を使用して、マネージド ディスクでカスタマー マネージド キーを使用し、サーバー側の暗号化を有効にする](../virtual-machines/disks-enable-customer-managed-keys-portal.md)」を参照してください。


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件を確認する。 
> * Azure Disk Encryption が有効になっている VM について、ソース リージョンのキー コンテナーから宛先リージョンのキー コンテナーにキーとシークレットをコピーする。
> * VM を移動に備えて準備し、ソース リージョンから移動するリソースを選択する。
> * リソースの依存関係を解決する。
> * Azure Disk Encryption が有効になっている VM について、宛先キー コンテナーを手動で割り当てる。 カスタマー マネージド キーによるサーバー側暗号化が使用されている VM について、宛先リージョンのディスク暗号化セットを手動で割り当てる。
> * キー コンテナーまたはディスク暗号化セットを移動する。
> * ソース リソース グループを準備して移動する。 
> * 他のリソースを準備して移動する。
> * 移動を破棄するか、コミットするかを決定する。 
> * 必要に応じて、移動後にソース リージョンのリソースを削除する。

> [!NOTE]
> このチュートリアルでは、シナリオを試すためのごく簡単な方法を紹介します。 使用しているのは既定のオプションのみです。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。 次に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="prerequisites"></a>前提条件

要件 |詳細
--- | ---
**サブスクリプションのアクセス許可** | 移動するリソースを含むサブスクリプションの "*所有者*" アクセス権があることを確認します。<br/><br/> *所有者アクセスが必要な理由:* Azure サブスクリプションの特定のソースと宛先のペアに対してリソースを初めて追加すると、Resource Mover によって、[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (旧称: Managed Service ID (MSI)) が作成されます。 この ID は、サブスクリプションによって信頼されます。 この ID を作成して、必要なロール (ソース サブスクリプションの "*共同作成者*" と "*ユーザー アクセス管理者*") をそれに割り当てるためには、リソースを追加する際に使用するアカウントに、サブスクリプションの "*所有者*" アクセス許可が必要です。 詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)」を参照してください。
**VM のサポート** | 移動する VM がサポート対象であることを、次のようにして確認します。<li>サポートされている Windows VM を[確認](support-matrix-move-region-azure-vm.md#windows-vm-support)します。<li>サポートされている Linux VM とカーネルのバージョンを[確認](support-matrix-move-region-azure-vm.md#linux-vm-support)します。<li>サポートされている[コンピューティング](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[ストレージ](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)、[ネットワーク](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)の設定を確認します。
**キー コンテナーの要件 (Azure Disk Encryption)** | VM の Azure Disk Encryption を有効にしている場合、ソースと宛先の両方のリージョンにキー コンテナーが必要です。 詳細については、[キー コンテナーの作成](../key-vault/general/quick-create-portal.md)に関するページを参照してください。<br/><br/> ソース リージョンと宛先リージョンのキー コンテナーについて、次のアクセス許可が必要になります。<li>キーのアクセス許可: キーの管理操作 (取得、一覧表示)、暗号化操作 (暗号化解除、暗号化)<li>シークレットのアクセス許可: シークレットの管理操作 (取得、一覧表示、設定)<li>証明書 (一覧表示、取得)
**ディスク暗号化セット (CMK によるサーバー側暗号化)** | CMK によるサーバー側暗号化が使用されている VM を使っている場合、ソースと宛先の両方のリージョンにディスク暗号化セットが必要です。 詳細については、[ディスク暗号化セットの作成](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)に関するページを参照してください。<br/><br/> カスタマー マネージド キーにハードウェア セキュリティ モジュール (HSM キー) を使用している場合、リージョン間の移動はサポートされません。
**ターゲット リージョンのクォータ** | サブスクリプションには、ターゲット リージョンで移動するリソースを作成するのに十分なクォータが必要です。 クォータがない場合は、[追加の制限を要求](../azure-resource-manager/management/azure-subscription-service-limits.md)します。
**ターゲット リージョンの料金** | VM の移動先となるターゲット リージョンに関連付けられている価格と料金を確認します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してください。


## <a name="verify-permissions-in-the-key-vault"></a>キー コンテナーにおけるアクセス許可を確認する

Azure Disk Encryption が有効になっている VM を移動する場合、「[宛先キー コンテナーにキーをコピーする](#copy-the-keys-to-the-destination-key-vault)」セクションに記載のスクリプトを実行する必要があります。 スクリプトを実行するユーザーには、そのための適切なアクセス許可が必要です。 必要なアクセス許可については、次の表を参照してください。 Azure portal で目的のキー コンテナーに移動すると、アクセス許可を変更するためのオプションが見つかります。 **[設定]** の **[アクセス ポリシー]** を選択します。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="キー コンテナーの [設定] ペインにある [アクセス ポリシー] リンクのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

ユーザーのアクセス許可がない場合は、 **[アクセス ポリシーの追加]** を選択してアクセス許可を指定します。 ユーザー アカウントに既にポリシーがある場合は、 **[ユーザー]** で、後述の表の手順に従ってアクセス許可を設定します。

Azure Disk Encryption を使用する Azure VM には、次のようなバリエーションが考えられます。対象とするコンポーネントに応じてアクセス許可を設定する必要があります。 想定される VM は次のとおりです。
- 既定のオプション (ディスクはシークレットのみを使用して暗号化される)。
- [キー暗号化キー (KEK)](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek) を使用してセキュリティが強化されている。

### <a name="source-region-key-vault"></a>ソース リージョンのキー コンテナー

スクリプトを実行するユーザーに対して、次のコンポーネントのアクセス許可を設定します。 

コンポーネント | 必要なアクセス許可
--- | ---
シークレット |  *Get* <br></br> **[シークレットのアクセス許可]**  >  **[シークレットの管理操作]** を選択し、 **[取得]** を選択します。 
[キー] <br></br> KEK を使用している場合、シークレットのアクセス許可に加えて、これらのアクセス許可が必要となります。 | "*取得*" と "*暗号化解除*" <br></br> **[キーのアクセス許可]**  >  **[キーの管理操作]** を選択し、 **[取得]** を選択します。 **[暗号化操作]** で、 **[暗号化解除]** を選択します。

### <a name="destination-region-key-vault"></a>宛先リージョンのキー コンテナー

**[アクセス ポリシー]** で、 **[Azure Disk Encryption (ボリューム暗号化用)]** が有効になっていることを確認します。 

スクリプトを実行するユーザーに対して、次のコンポーネントのアクセス許可を設定します。 

コンポーネント | 必要なアクセス許可
--- | ---
シークレット |  *Set* <br></br> **[シークレットのアクセス許可]**  >  **[シークレットの管理操作]** を選択し、 **[設定]** を選択します。 
[キー] <br></br> KEK を使用している場合、シークレットのアクセス許可に加えて、これらのアクセス許可が必要となります。 | "*取得*"、"*作成*"、"*暗号化*" <br></br> **[キーのアクセス許可]**  >  **[キーの管理操作]** を選択し、 **[取得]** および **[作成]** を選択します。 **[暗号化操作]** で **[暗号化]** を選択します。

<br>

前述のアクセス許可に加え、Resource Mover が皆さんに代わって Azure リソースへのアクセスに使用する[マネージド システム ID](./common-questions.md#how-is-managed-identity-used-in-resource-mover) のアクセス許可を宛先キー コンテナーに追加する必要があります。 

1. **[設定]** の **[アクセス ポリシーの追加]** を選択します。 
1. **[プリンシパルの選択]** で MSI を検索します。 MSI の名前は ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` です。 
1. MSI に関しては、次のアクセス許可を追加します。

    コンポーネント | 必要なアクセス許可
    --- | ---
    シークレット|  "*取得*" と "*一覧*" <br></br> **[シークレットのアクセス許可]**  >  **[シークレットの管理操作]** を選択し、 **[取得]** および **[一覧]** を選択します。 
    [キー] <br></br> KEK を使用している場合、シークレットのアクセス許可に加えて、これらのアクセス許可が必要となります。 | "*取得*" と "*一覧*" <br></br> **[キーのアクセス許可]**  >  **[キーの管理操作]** を選択し、 **[取得]** および **[一覧]** を選択します。

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>宛先キー コンテナーにキーをコピーする

提供されている[スクリプト](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)を使用して、暗号化のシークレットとキーをソース キー コンテナーから宛先キー コンテナーにコピーします。

- PowerShell でスクリプトを実行します。 最新バージョンの PowerShell を使用することをお勧めします。
- このスクリプトには、具体的に次のモジュールが必要となります。
    - Az.Compute
    - Az.KeyVault (バージョン 3.0.0)
    - Az.Accounts (バージョン 2.2.3)

スクリプトを実行するには次のようにします。

1. GitHub にある[スクリプト](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)を開きます。
1. スクリプトの内容をローカル ファイルにコピーし、*Copy-keys.ps1* という名前を付けます。
1. スクリプトを実行します。
1. Azure portal にサインインします。
1. **[User Inputs]\(ユーザー入力\)** ウィンドウで、ソース サブスクリプション、リソース グループ、ソース VM を選択します。次に、ターゲットの場所を選択し、ディスク暗号化とキー暗号化に使用するターゲットのコンテナーを選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="スクリプトの値を入力するための [User Inputs]\(ユーザー入力\) ウィンドウのスクリーンショット。" :::

1. **[選択]** ボタンを選択します。 
   
   スクリプトの実行が完了すると、CopyKeys が成功したことを伝えるメッセージが表示されます。

## <a name="prepare-vms"></a>VM を準備する

1. VM が[前提条件](#prerequisites)を満たしていることを確認したら、移動する VM を確実にオンにします。 宛先リージョンで利用するすべての VM ディスクを VM にアタッチし、初期化する必要があります。
1. 信頼された最新のルート証明書と、更新された証明書失効リスト (CRL) が VM にあることを確認するために、次のことを行います。
    - Windows VM で、最新の Windows 更新プログラムをインストールします。
    - Linux VM では、ディストリビューター ガイダンスに従って、マシンに最新の証明書と CRL が存在するようにします。 
1. VM からのアウトバウンド接続を許可するために、次のどちらかを実行します。
    - アウトバウンド接続を制御するために URL ベースのファイアウォール プロキシを使用する場合は、[URL へのアクセスを許可](support-matrix-move-region-azure-vm.md#url-access)します。
    - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御する場合は、これらの[サービス タグ ルール](support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

## <a name="select-the-resources-to-move"></a>移動するリソースを選択する

- 選択したソース リージョン内の任意のリソース グループから、サポートされている任意のリソースの種類を選択できます。  
- ソース リージョンと同じサブスクリプション内のターゲット リージョンにリソースを移動できます。 サブスクリプションを変更する場合は、リソースの移動後に変更を行うことができます。

リソースを選択するには、次の手順を実行します。

1. Azure portal で、**Resource Mover** を検索します。 その後、 **[サービス]** で **[Azure Resource Mover]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Azure portal の Azure Resource Mover の検索結果のスクリーンショット。" :::

1. Azure Resource Mover の **[概要]** ペインで、 **[Move across regions]\(リージョン間で移動する\)** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="別のリージョンに移動するリソースを追加するための [Move across regions]\(リージョン間で移動する\) ボタンのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. **[リソースの移動]** ペインの **[Source + destination]\(ソース + 宛先\)** タブを選択し、移動元のサブスクリプションとリージョンをドロップダウン リストから選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="ソースと宛先のリージョンを選択するページ。" :::

1. **[Destination]\(宛先\)** で、VM の移動先となるリージョンを選択し、 **[次へ]** を選択します。

1. **[移動するリソース]** タブを選択し、 **[リソースの選択]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="[リソースの移動] ペインと [リソースの選択] ボタンのスクリーンショット。" :::

1. **[リソースの選択]** ペインで、移動する VM を選択します。 「[移動するリソースを選択する](#select-the-resources-to-move)」セクションで述べたように、追加できるのは、移動の対象としてサポートされるリソースのみです。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="移動する VM を選択するための [リソースの選択] ペインのスクリーンショット。" :::

    > [!NOTE]
    >  このチュートリアルでは、カスタマー マネージド キーによるサーバー側暗号化を使用する VM (rayne-vm) と、ディスク暗号化が有効になった VM (rayne-vm-ade) を選択します。

1. **[Done]** を選択します。
1. **[移動するリソース]** タブを選択し、 **[次へ]** を選択します。
1. **[Review]\(レビュー\)** タブを選択し、ソースと宛先の設定を確認します。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="ソースと宛先の設定を確認するためのペインのスクリーンショット。" :::

1. **[続行]** を選択して、リソースの追加を開始します。
1. 進行状況を追跡するための通知アイコンを選択します。 プロセスが正常に完了した後、 **[通知]** ペインの **[Added resources for move]\(移動対象に追加されたリソース\)** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="リソースが正常に追加されたことを確認するための [通知] ペインのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. 通知を選択した後、 **[Across regions]\(リージョン間\)** ページでリソースを確認します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="&quot;準備が保留中&quot; 状態となっている追加されたリソースのスクリーンショット。" :::

> [!NOTE]
> - 追加したリソースは "*準備が保留中*" の状態になります。
> - VM のリソース グループが自動的に追加されます。
> - 宛先リージョン内の既存のリソースを使用するように **[Destination configuration]\(宛先の構成\)** エントリを変更する場合、そのリソースについては移動を開始する必要がないため、リソースの状態が "*コミット保留中*" に設定されます。
> - 追加済みのリソースを削除する場合、その方法は、移動プロセスのどの段階にあるかによって異なります。 詳細については、「[移動コレクションとリソース グループを管理する](remove-move-resources.md)」を参照してください。


## <a name="resolve-dependencies"></a>依存関係を解決する

1. いずれかのリソースの **[問題]** 列に *[依存関係の検証]* メッセージが表示されている場合は、 **[依存関係の検証]** ボタンを選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="[依存関係の検証] ボタンを示すスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    検証プロセスが開始されます。
1. 依存関係が見つかった場合は、 **[依存関係の追加]** を選択します。  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="[依存関係の追加] ボタンのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. **[依存関係の追加]** ペインは、既定の **[Show all dependencies]\(すべての依存関係を表示する\)** オプションのままにしてください。

    - **[Show all dependencies]\(すべての依存関係を表示する\)** を選択すると、リソースの直接の依存関係と間接の依存関係がすべて反復処理されます。 たとえば、VM について、NIC、仮想ネットワーク、ネットワーク セキュリティ グループ (NSG) などが表示されます。
    - **[Show first level dependencies only]\(第 1 レベルの依存関係のみ表示する\)** を選択すると、直接の依存関係のみが表示されます。 たとえば、VM について、NIC は表示されますが、仮想ネットワークは表示されません。
 
1. 追加する依存リソースを選択し、 **[依存関係の追加]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="依存関係リストと [依存関係の追加] ボタンのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. もう一度、依存関係を検証します。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="依存関係を再検証するためのペインのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>宛先のリソースを割り当てる

暗号化に関連する宛先リソースは、手動で割り当てる必要があります。

- Azure Disk Encryption が有効になっている VM を移動する場合、宛先リージョン内のキー コンテナーが依存関係として表示されます。
- CMK によるサーバー側暗号化が使用されている VM を移動する場合は、宛先リージョン内のディスク暗号化セットが依存関係として表示されます。 
- このチュートリアルでは、Azure Disk Encryption が有効にされている VM と CMK が使用されている VM の移動について取り上げているため、宛先のキー コンテナーとディスク暗号化セットの両方が依存関係として表示されます。

宛先のリソースを手動で割り当てるには、次の手順を実行します。

1. ディスク暗号化セットのエントリで、 **[Destination configuration]\(宛先の構成\)** 列の **[Resource not assigned]\(リソースが割り当てられていません\)** を選択します。
1. **[構成設定]** で、宛先のディスク暗号化セットを選択し、 **[Save changes]\(変更の保存\)** を選択します。
1. 変更しているリソースの依存関係を保存して検証するか、単に変更を保存し、変更したものすべてを一度に検証することができます。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="宛先リージョンの変更を保存するための [Destination configuration]\(宛先の構成\) ペインのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    宛先リソースを追加すると、ディスク暗号化セットの状態が "*移動のコミットが保留中*" に変わります。

1. キー コンテナーのエントリで、 **[Destination configuration]\(宛先の構成\)** 列の **[Resource not assigned]\(リソースが割り当てられていません\)** を選択します。 **[構成設定]** で宛先のキー コンテナーを選択し、変更内容を保存します。 

この段階で、ディスク暗号化セットとキー コンテナーの状態が "*移動のコミットが保留中*" に変わります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="他のリソースを準備するためのペインのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

暗号化リソースの移動プロセスをコミットして完了するには、次のようにします。

1. **[Across regions]\(リージョン間\)** でリソース (ディスク暗号化セットまたはキー コンテナー) を選択し、 **[Commit move]\(移動のコミット\)** を選択します。
1. **[リソースの移動]** で、 **[コミット]** を選択します。

> [!NOTE]
> 移動をコミットすると、リソースの状態が "*ソースの削除が保留中*" に変わります。


## <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する 

VM の準備と移動を行う前に、VM リソース グループがターゲット リージョンに存在している必要があります。 

### <a name="prepare-to-move-the-source-resource-group"></a>ソース リソース グループの移動を準備する

準備プロセス中に、Resource Mover によって、リソース グループの設定から Azure Resource Manager (ARM) テンプレートが生成されます。 リソース グループ内のリソースは影響を受けません。

ソース リソース グループを移動するための準備として、次の手順を実行します。

1. **[Across regions]\(リージョン間\)** で、ソース リソース グループを選択し、 **[準備]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="[リソースの準備] ペインの [準備] ボタンのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. **[リソースの準備]** で、 **[準備]** を選択します。

> [!NOTE]
> 移動の準備が完了すると、リソースグループの状態が "*移動の開始が保留中*" に変わります。 

 
### <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する

次のようにして、ソース リソース グループの移動を開始します。

1. **[Across regions]\(リージョン間\)** ペインで、リソース グループを選択し、 **[移動の開始]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="[Across regions]\(リージョン間\) ペインの [移動の開始] ボタンのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. **[リソースの移動]** ペインで **[移動の開始]** を選択します。 リソース グループの状態が "*移動の開始が進行中*" に変わります。   
1. 移動を開始した後、生成された ARM テンプレートに基づいて、ターゲット リソース グループが作成されます。 ソース リソース グループの状態が "*移動のコミットが保留中*" に変わります。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="リソース グループの状態が &quot;移動のコミットが保留中&quot; に変化したことを示す [リソースの移動] ペインのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

移動をコミットしてプロセスを完了するには、次のようにします。

1. **[Across regions]\(リージョン間\)** ペインで、リソース グループを選択し、 **[Commit move]\(移動のコミット\)** を選択します。
1. **[リソースの移動]** ペインで **[コミット]** を選択します。

> [!NOTE]
> 移動をコミットすると、ソース リソース グループの状態が "*ソースの削除が保留中*" に変わります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="状態が &quot;ソースの削除が保留中&quot; に変化したことを示すソース リソース グループのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>移動するリソースを準備する

暗号化リソースとソース リソース グループを移動したら、"*準備が保留中*" の状態になっている他のリソースを移動するための準備を行うことができます。


1. **[Across regions]\(リージョン間\)** ペインで移動を再検証し、問題があれば解決します。
1. 移動を開始する前にターゲット設定を編集する場合は、リソースの **[Destination configuration]\(宛先の構成\)** 列にあるリンクを選択し、その設定を編集します。 ターゲット VM の設定を編集する場合、ターゲット VM のサイズをソース VM のサイズより小さくすることはできません。
1. "*準備が保留中*" 状態になっている移動対象のリソースについて、 **[準備]** を選択します。
1. **[リソースの準備]** ペインで、 **[準備]** を選択します。

    - 準備中に、Azure Site Recovery モビリティ エージェントが、レプリケートする VM にインストールされます。
    - VM データは、ターゲット リージョンに定期的にレプリケートされます。 これはソース VM には影響しません。
    - Resource Mover によって、他のソース グループ用の ARM テンプレートが生成されます。

> [!NOTE]
> リソースの準備が完了すると、その状態が "*移動の開始が保留中*" に変わります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="&quot;移動の開始が保留中&quot; 状態のリソースが表示されている [リソースの準備] ペインのスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>移動を開始する

リソースの準備が完了したら、移動を開始できます。 

1. **[Across regions]\(リージョン間\)** ペインで、"*移動の開始が保留中*" 状態のリソースを選択し、 **[移動の開始]** を選択します。
1. **[リソースの移動]** ペインで **[移動の開始]** を選択します。
1. 通知バーで移動の進行状況を追跡します。

    - VM の場合、レプリカ VM はターゲット リージョンに作成されます。 ソース VM がシャットダウンされ、ダウンタイム (通常は数分) が発生します。
    - Resource Mover によって、準備された ARM テンプレートを使用して他のリソースが再作成されます。 通常、ダウンタイムは発生しません。
    - リソースを移動すると、その状態が "*移動のコミットが保留中*" に変わります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="&quot;移動のコミットが保留中&quot; 状態のリソース一覧のスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>破棄またはコミットしますか?

最初の移動後に、移動をコミットするか、破棄するかを決定できます。 

- **破棄**: テストを行っていて、実際にはソース リソースを移動したくない場合は移動を破棄します。 移動を破棄すると、リソースが "*移動の開始が保留中*" の状態に戻ります。
- **コミット**:コミットすると、ターゲット リージョンへの移動が完了します。 ソース リソースをコミットすると、その状態が "*ソースの削除が保留中*" になり、削除するかどうかを決定できます。


## <a name="discard-the-move"></a>移動を破棄する 

移動を破棄するには、次の手順を実行します。

1. **[Across regions]\(リージョン間\)** ペインで、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[移動の破棄]** を選択します。
1. **[移動の破棄]** ペインで、 **[破棄]** を選択します。
1. 通知バーで移動の進行状況を追跡します。


> [!NOTE]
> リソースを破棄すると、VM の状態が "*移動の開始が保留中*" に変わります。

## <a name="commit-the-move"></a>移動をコミットする

移動プロセスを完了するために、次の手順に従って移動をコミットします。 

1. **[Across regions]\(リージョン間\)** ペインで、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[移動のコミット]** を選択します。
1. **[Commit resources]\(リソースのコミット\)** ペインで **[コミット]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="コミットして移動を完了するリソースの一覧のスクリーンショット。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. 通知バーでコミットの進行状況を追跡します。

> [!NOTE]
> - 移動をコミットすると、VM のレプリケーションが停止します。 ソース VM はコミットの影響を受けません。
> - コミットプロセスは、ソース ネットワーク リソースには影響しません。
> - 移動をコミットすると、リソースの状態が "*ソースの削除が保留中*" に変わります。



## <a name="configure-settings-after-the-move"></a>移動後に設定を構成する

- モビリティ サービスは、VM から自動的にはアンインストールされません。 手動でアンインストールします。あるいは、サーバーをもう一度移動する予定であれば、そのままにしておきます。
- 移動後、Azure ロールベースのアクセス制御 (RBAC) 規則を変更します。

## <a name="delete-source-resources-after-commit"></a>コミット後にソース リソースを削除する

移動後に、必要に応じて、ソース リージョンのリソースを削除できます。 

1. **[Across regions]\(リージョン間\)** ペインで、削除する各ソース リソースを選択し、 **[ソースの削除]** を選択します。
1. **[ソースの削除]** で、削除しようとしているリソースを確認し、 **[削除の確認]** で「**yes**」と入力します。 この操作は元に戻せません。十分に確認してください。
1. 「**yes**」と入力したら、 **[ソースの削除]** を選択します。

> [!NOTE]
>  Resource Move ポータルで、リソース グループ、キー コンテナー、SQL Server インスタンスを削除することはできません。 各リソースのプロパティ ページから個別に削除する必要があります。


## <a name="delete-resources-that-you-created-for-the-move"></a>移動用に作成されたリソースを削除する

このプロセス中に作成した Site Recovery リソースと移動コレクションは、移動後に手動で削除できます。

- 既定では、移動コレクションは非表示になっています。 これを表示するには、非表示のリソースを有効にする必要があります。
- キャッシュ ストレージには、事前に削除しておく必要があるロックがあります。

リソースを削除するには、次の手順を実行します。 
1. リソース グループ ```RegionMoveRG-<sourceregion>-<target-region>``` でリソースを見つけます。
1. ソース リージョン内のすべての VM とその他のソース リソースが移動または削除されていることを確認します。 この手順により、それらを使用している保留中のリソースがないことが保証されます。
1. リソースを削除します。

    - 移動コレクションの名前: ```movecollection-<sourceregion>-<target-region>```
    - キャッシュ ストレージ アカウントの名前: ```resmovecache<guid>```
    - コンテナー名: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 暗号化された Azure VM とその依存リソースを別の Azure リージョンに移動しました。


次のステップでは、Azure SQL データベースとエラスティック プールを別のリージョンに移動してみます。

> [!div class="nextstepaction"]
> [Azure SQL リソースを移動する](./tutorial-move-region-sql.md)
