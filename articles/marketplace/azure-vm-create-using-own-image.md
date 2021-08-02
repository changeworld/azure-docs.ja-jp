---
title: 独自のイメージを使用して Azure Marketplace に Azure 仮想マシン オファーを作成する
description: 独自のイメージを使用して Azure Marketplace に仮想マシン オファーを公開します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 06/02/2021
ms.openlocfilehash: 44a31ee5fc8118ad163b5edb582b2a182b75dd3e
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525014"
---
# <a name="create-a-virtual-machine-using-your-own-image"></a>独自のイメージを使用して仮想マシンを作成する

この記事では、オンプレミスで構築した仮想マシン (VM) イメージを発行する方法について説明します。

## <a name="bring-your-image-into-azure"></a>イメージを Azure に取り込む

VHD を Azure 共有イメージ ギャラリーにアップロードします。

1. Azure portal で、 **[共有イメージギャラリー]** を検索します。
2. 既存の共有イメージ ギャラリーを作成または使用します。 Marketplace に発行されるイメージには、別の共有イメージ ギャラリーを作成することをお勧めします。
3. 既存のイメージ定義を作成または使用します。
4. **[バージョンの作成]** を選択します。
5. リージョンとイメージのバージョンを選択します。
6. VHD がまだ Azure portal にアップロードされていない場合は、**ソース** として **[ストレージ blob (VHD)]** を選択し、 **[参照]** をクリックします。 **ストレージ アカウント** と **ストレージ コンテナー** は、前に作成していない場合は作成できます。 VHD をアップロードします。
7. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が終了したら、 **[作成]** を選択します。

> [!TIP]
> SIG イメージを公開するには、公開元アカウントに "所有者" アクセス権が必要です。 必要に応じて、次のセクションの手順に従って、アクセス権を付与するための **適切なアクセス許可を設定** します。

## <a name="set-the-right-permissions"></a>適切なアクセス許可の選択

お使いのパートナー センターアカウントがサブスクリプションをホストしている共有イメージギャラリーの所有者であれば、それ以外のアクセス許可は必要ありません。

サブスクリプションへの読み取りアクセス権のみを持っている場合は、次の 2 つのオプションのいずれかを使用します。

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>オプション 1 – 所有者に所有者のアクセス許可を付与する

所有者に所有者のアクセス許可を付与する手順:

1. Azure Shared Image Gallery (SIG) にアクセスします。
2. 左側のパネルで **[アクセス制御 (IAM)]** を選択します。
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します。<br>
    :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="[ロールの割り当ての追加] ウィンドウが表示されます。":::
1. **[ロール]** で、 **[所有者]** を選択します。
1. **[アクセスの割り当て先]** で、 **[User, group, or service principle]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。
1. **[選択]** の場合、イメージを公開するユーザーの Azure メールアドレスを入力します。
1. **[保存]** を選択します。

### <a name="option-two--run-a-command"></a>オプション 2 – コマンドを実行する

所有者にこれらのコマンドのいずれかを実行するよう依頼します (どちらの場合も、共有イメージ ギャラリーを作成したサブスクリプションの SusbscriptionId を使用します)。

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
> パートナー センターで SIG イメージを公開できるようになったため、SAS URI を生成する必要はありません。 ただし、それでも SAS URI の生成手順を参照する必要がある場合は、「[VM イメージの SAS URI を生成する方法](azure-vm-get-sas-uri.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [お使いの VM イメージをテストし](azure-vm-image-test.md)、それが Azure Marketplace の公開要件を満たしていることを確認します (オプション)。
- VM イメージをテストしない場合は、 [パートナー センター](https://partner.microsoft.com/)にサインインし、SIG イメージを公開します。
- お使いの Azure ベースの新しい VHD の作成で問題が発生した場合は、[Azure Marketplace の VM に関する FAQ](azure-vm-create-faq.md) を参照してください。
