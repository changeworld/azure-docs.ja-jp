---
title: Azure Stack での管理者ポータルの使用 | Microsoft Docs
description: Azure Stack オペレーターが管理者ポータルを使用する方法を学習します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: mabrigg
ms.openlocfilehash: 58856875fa7d7bb3ba63c489fb17790e68f99aec
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872188"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Azure Stack の管理者ポータルの使用

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack には、管理者ポータルとユーザー ポータル (*テナント* ポータルとも呼ばれることもあります) の 2 つのポータルがあります。Azure Stack オペレーターは、管理者ポータルを使用して Azure Stack の日常的な管理と操作を行うことができます。

## <a name="access-the-administrator-portal"></a>管理者ポータルへのアクセス

開発キット環境の場合は、最初に、リモート デスクトップ接続または仮想プライベート ネットワーク (VPN) を使用して[開発キットのホストに接続](azure-stack-connect-azure-stack.md)できることを確認する必要があります。

管理者ポータルにアクセスするには、ポータルの URL を参照し、Azure Stack オペレーターの資格情報を使用してサインインします。 統合システムの場合は、ポータルの URL は Azure Stack のデプロイのリージョン名と外部 FQDN (完全修飾ドメイン名) によって異なります。

| 環境 | 管理者ポータルの URL |   
| -- | -- | 
| 開発キット| https://adminportal.local.azurestack.external  |
| 統合システム | https://adminportal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; | 
| | |

 ![管理者ポータル](media/azure-stack-manage-portals/admin-portal.png)

すべての Azure Stack デプロイの既定のタイム ゾーンは、協定世界時 (UTC) に設定されることに注意してください。 Azure Stack のインストール時にタイム ゾーンを選択できますが、インストール中に自動的に既定として UTC に戻されます。

管理者ポータルでは、次のような操作を行えます。

* インフラストラクチャ (システム正常性、更新プログラム、容量など) の管理
* Marketplace の設定
* ユーザーのサブスクリプションの作成
* プランとオファーの作成

**[クイック スタートのチュートリアル]** タイルに、最も一般的なタスクのオンライン ドキュメントへのリンクがあります。

オペレーターは、管理ポータルで仮想マシン、仮想ネットワーク、ストレージ アカウントなどのリソースを作成することができますが、リソースを作成してテストするためには、[ユーザー ポータルにサインイン](user/azure-stack-use-portal.md)する必要があります。

>[!NOTE]
>管理ポータルの [クイック スタートのチュートリアル] タイルにある **[仮想マシンの作成]** リンクから仮想マシンを作成できますが、これは最初のデプロイ後に Azure Stack を検証するときにのみ使用します。

## <a name="understand-subscription-behavior"></a>サブスクリプション操作の概要

管理者ポータルから利用できるサブスクリプションは 1 つだけです。 そのサブスクリプションは*既定プロバイダー サブスクリプション*です。 他のサブスクリプションを追加して管理者ポータルで使用することはできません。

Azure Stack オペレーターは、管理者ポータルからユーザー (自分自身を含む) のサブスクリプションを追加できます。 ユーザー (自分自身を含む) は、**ユーザー** ポータルからこれらのサブスクリプションにアクセスして使用できます。 ただし、ユーザー ポータルでは、管理者ポータルの管理機能または運用機能のいずれにもアクセスできません。

管理者ポータルとユーザー ポータルは、Azure Resource Manager の個別のインスタンスでサポートされます。 このように Resource Manager が別々になっているため、ポータルをまたいでサブスクリプションを使用することはできません。 たとえば、Azure Stack オペレーターとしてユーザー ポータルにサインインした場合は、*既定プロバイダー サブスクリプション*にアクセスすることはできません。 管理機能にアクセスすることはできませんが、利用できる公開オファーから自分でサブスクリプションを作成することはできます。 ユーザー ポータルにサインインしている限り、テナント ユーザーと見なされます。

  >[!NOTE]
  >開発キット環境では、ユーザーが Azure Stack オペレーターと同じテナント ディレクトリに属している場合、ユーザーは管理者ポータルへのサインインをブロックされません。 ただし、どの管理機能にもアクセスできません。 また、管理者ポータルからは、サブスクリプションを追加することも、ユーザー ポータルで利用できるプランにアクセスすることもできません。

## <a name="administrator-portal-tips"></a>管理者ポータルに関するヒント

### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ

ダッシュボードには、既定のタイルのセットが含まれています。 **[ダッシュボードの編集]** を選択して既定のダッシュボードを変更したり、**[新しいダッシュボード]** を選択してカスタムのダッシュボードを追加したりできます。 ダッシュボードにはタイルを簡単に追加できます。 たとえば、**[+ リソースの作成]** を選択し、**[オファー + プラン]** を右クリックし、**[ダッシュボードにピン留めする]** を選択します。

ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを回復するには、**[ダッシュボードの編集]** をクリックし、右クリックして、**[既定の状態にリセット]** を選びます。

### <a name="quick-access-to-online-documentation"></a>オンライン ドキュメントへのクイック アクセス

Azure Stack オペレーター向けドキュメントにアクセスするには、管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) を使用します。 カーソルをアイコンに移動し、**[ヘルプとサポート]** を選択します。

### <a name="quick-access-to-help-and-support"></a>ヘルプとサポートへのクイック アクセス

管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) を選択し、**[新しいサポート要求]** を選択すると、次のいずれかの結果になります。

- 統合システムを使用している場合は、Microsoft カスタマー サポート サービス (CSS) のサポート チケットを直接開くことができるサイトが開きます。 Microsoft サポートと OEM (Original Equipment Manufacturer) ハードウェア ベンダーのサポートのどちらに連絡するかについては、「[サポートが受けられる場所](azure-stack-manage-basics.md#where-to-get-support)」を参照してください。
- 開発キットを使用している場合は、Azure Stack フォーラムのサイトが直接開きます。 これらのフォーラムは定期的にチェックされています。 開発キットは評価環境であるため、Microsoft CSS を通した正式なサポートは提供されていません。

### <a name="quick-access-to-the-azure-roadmap"></a>Azure ロードマップへのクイック アクセス

管理者ポータルの右上隅にある**ヘルプとサポート** (疑問符) を選択し、**[Azure ロードマップ]** を選択すると、新しいブラウザー タブが開き、Azure ロードマップが表示されます。 **[製品]** 検索ボックスに **Azure Stack** と入力すると、すべての Azure Stack ロードマップの更新プログラムを確認できます。

## <a name="next-steps"></a>次の手順

- [Azure Stack でのリージョンの管理](azure-stack-region-management.md)
