---
title: 移行方法 - ポータル
description: Azure portal を使用して Cloud Services (延長サポート) に移行する方法
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286706"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal を使用して Cloud Services (延長サポート) に移行する

この記事では、Azure portal を使用して [Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md) から [Cloud Services (延長サポート)](overview.md) に移行する方法について説明します。

> [!IMPORTANT]
> 移行ツールを使用した Cloud Services (クラシック) から Cloud Services への移行 (延長サポート) は、現在、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="before-you-begin"></a>始める前に

**サブスクリプションの管理者であることを確実にします。**

この移行を実行するには、[Azure portal](https://portal.azure.com) で自分をサブスクリプションの共同管理者として追加する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[ハブ]** メニューで、 **[サブスクリプション]** を選択します。 表示されない場合は、 **[すべてのサービス]** を選択します。
3. 適切なサブスクリプションのエントリを探し、 **[自分のロール]** フィールドを確認します。 共同管理者の場合、この値は *[アカウント管理者]* です。

共同管理者を追加できない場合は、サービス管理者またはサブスクリプションの[共同管理者](../role-based-access-control/classic-administrators.md)に連絡して、追加を依頼します。

**移行リソース プロバイダーにサインアップする**

1. [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1) を使用して、Microsoft.Compute 名前空間で移行リソース プロバイダー `Microsoft.ClassicInfrastructureMigrate` とプレビュー機能 `Cloud Services` に登録します。  
1. 登録が完了するまで 5 分待ってから、承認の状態を確認します。 

## <a name="migrate-your-cloud-service-resources"></a>クラウド サービスのリソースを移行する

1. [Cloud Services (クラシック) ポータル ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames)に移動します。 
2. 移行するクラウド サービスを選択します。
3. **[ARM への移行]** ブレードを選択します。

    > [!NOTE]
    > 仮想ネットワーク (クラシック) 内にある Cloud Services (クラシック) を移行する場合、仮想ネットワーク (クラシック) ブレードの移動を求めるバナー メッセージが表示されます。
    > 仮想ネットワーク (クラシック) ブレードに移動するので、その中の仮想ネットワーク (クラシック) と Cloud Services (クラシック) のデプロイの両方の移行を完了します。
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Azure portal でのクラシック仮想ネットワークの移動を示す画像。":::
 

4. 移行を検証します。 

    検証が成功した場合、すべてのデプロイがサポートされ、準備できる状態になっています。  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Azure portal の [ARM への移行] ブレードを示す画像。":::

    検証が失敗した場合、サポートされていないシナリオの一覧が表示されるので、移行を続ける前に修正する必要があります。 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Azure portal での検証エラーを示す画像。":::

5. 移行を準備します。

    準備が正常に完了したら、移行をコミットできる状態になります。
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Azure portal での検証合格を示す画像。":::

    準備が失敗した場合は、エラーを確認し、問題を解決してから、もう一度準備を試してみて下さい。 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="検証失敗エラーを示す画像。":::

      準備が完了すると、仮想ネットワーク内のすべてのクラウド サービスを、Cloud Services (クラシック) と Cloud Services (延長サポート) 両方の Azure portal ブレードを使用して、読み取ることができるようになります。 移行を完了する前に、クラウド サービス (拡張サポート) のデプロイをテストすると、正常に機能していることを確保できます。 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="ポータルのブレードでの API のテストを示す画像。":::

6.  **(省略可能)** 移行を中止します。 
    
    移行を中止することを選択した場合は、 **[中止]** ボタンを使用して、これまでの手順をロールバックします。 その後、Cloud Services (クラシック) のデプロイは、すべての CRUD 操作についてロック解除されます。

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="検証の合格を示す画像。":::

    中止が失敗した場合は、 **[Retry abort]\(中止の再試行\)** を選択します。 再試行によって問題は解決されるはずです。 そうでない場合は、サポートにお問い合わせください。 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="検証失敗エラー メッセージを示す画像。":::

7.  移行をコミットします。

    >[!IMPORTANT]
    > 移行をコミットした後は、ロールバックすることはできません。 
    
    「yes」と入力して確認し、移行をコミットします。 移行が完了します。 移行された Cloud Services (延長サポート) のデプロイで、すべての操作がロック解除されます。 

## <a name="next-steps"></a>次の手順
[移行後の変更](in-place-migration-overview.md#post-migration-changes)に関するセクションを参照し、新しい Cloud Services (延長サポート) のデプロイのデプロイ ファイル、自動化、その他の属性での変更を確認します。 
