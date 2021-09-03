---
title: インクルード ファイル
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: b1eb954626570d7feb2af7fe0980e4f7a10e70c6
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113280053"
---
## <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 これを実現するには、オペレーティング システム VHD を一般化する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

### <a name="for-windows"></a>Windows の場合

Windows OS ディスクは、[sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) ツールを使用して一般化されます。 後で OS を更新または再構成する場合は、sysprep を再び実行する必要があります。

> [!WARNING]
> 更新プログラムが自動的に実行される可能性があるため、sysprep を実行した後、デプロイされるまでは VM をオフにしてください。 このシャットダウンで、以降の更新プログラムによるオペレーティング システムまたはインストール済みサービスへのインスタンス固有の変更が行われなくなります。 sysprep の実行の詳細については、[Windows VM の一般化](../../virtual-machines/generalize.md#windows)に関するページを参照してください。

### <a name="for-linux"></a>Linux の場合

1. Azure Linux エージェントを削除します。
    1. SSH クライアントを使って Linux VM に接続します。
    2. SSH ウィンドウで、コマンド `sudo waagent –deprovision+user` を入力します。
    3. 「Y」と入力して続行します (前のコマンドに -force パラメーターを追加すると、この確認手順を省略できます)。
    4. コマンドが完了したら、「**Exit**」と入力して SSH クライアントを閉じます。
2. 仮想マシンを停止します。
    1. Azure portal でリソース グループ (RG) を選択し、VM の割り当てを解除します。
    2. これで VM が一般化されたため、この VM ディスクを使用して新しい VM を作成できます。

### <a name="capture-image"></a>イメージをキャプチャする

> [!NOTE]
> 公開するには、SIG を含む Azure サブスクリプションが、公開元アカウントと同じテナントにある必要があります。 また、公開元アカウントには、SIG を含むサブスクリプションへの共同作成者のアクセス権が少なくとも必要です。

VM の準備ができたら、Azure Shared Image Gallery でキャプチャできます。 キャプチャするには、次の手順に従います。

1. [Azure portal](https://ms.portal.azure.com/) で、使用している仮想マシンのページにアクセスします。
2. **[キャプチャ]** を選択します。
3. **[Shared Image Gallery にイメージを共有する]** で、 **[Yes, share it to a gallery as an image version]\(はい、ギャラリーにイメージ バージョンとして共有します\)** を選択します。
4. **[Operating system state]\(オペレーティング システムの状態\)** で、[一般化] を選択します。
5. ターゲット イメージ ギャラリーまたは **[新規作成]** を選択します。
6. ターゲット イメージ定義または **[新規作成]** を選択します。
7. イメージの **[バージョン番号]** を指定します。
8. **[確認と作成]** を選択して、選択内容を確認します。
9. 検証に合格したら、 **[作成]** を選択します。

## <a name="set-the-right-permissions"></a>適切なアクセス許可を選択する

お使いのパートナー センター アカウントが、Shared Image Gallery をホストしているサブスクリプションの所有者であれば、それ以外のアクセス許可は必要ありません。

サブスクリプションへの読み取りアクセス権のみを持っている場合は、次の 2 つのオプションのいずれかを使用します。

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>オプション 1 – 所有者に所有者のアクセス許可を付与するよう依頼する

所有者が所有者のアクセス許可を付与するための手順:

1. Azure Shared Image Gallery (SIG) にアクセスします。
2. 左側のパネルで **[アクセス制御 (IAM)]** を選択します。
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します。<br>
    :::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="[ロールの割り当ての追加] ウィンドウが表示されます。":::
1. **[ロール]** で、 **[所有者]** を選択します。
1. **[アクセスの割り当て先]** で、 **[User, group, or service principle]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。
1. **[選択]** の場合、イメージを公開するユーザーの Azure メールアドレスを入力します。
1. **[保存]** を選択します。

### <a name="option-two--run-a-command"></a>オプション 2 – コマンドを実行する

所有者にこれらのコマンドのいずれかを実行するよう依頼します (どちらの場合も、Shared Image Gallery を作成したサブスクリプションの SusbscriptionId を使用します)。

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```
 
```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> パートナー センターで SIG イメージを公開できるようになったため、SAS URI を生成する必要はありません。 ただし、それでも SAS URI の生成手順を参照する必要がある場合は、「[VM イメージの SAS URI を生成する方法](../azure-vm-get-sas-uri.md)」を参照してください。
