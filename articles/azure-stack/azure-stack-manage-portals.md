---
title: Azure Stack での管理ポータルの使用 | Microsoft Docs
description: Azure Stack オペレーターとして管理ポータルを使用する方法を学習します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 8662cfde881a90ce8f7fc6cceaa576d3b971d9d6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817934"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>クイック スタート: Azure Stack 管理ポータルを使用する

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack には、管理ポータルとユーザー ポータル ("*テナント*" ポータルとも呼ばれることもあります) の 2 つのポータルがあります。Azure Stack オペレーターは、管理ポータルを使用して Azure Stack の日常的な管理と操作を行います。

## <a name="access-the-administrator-portal"></a>管理者ポータルへのアクセス

管理者ポータルにアクセスするには、ポータルの URL を参照し、Azure Stack オペレーターの資格情報を使用してサインインします。 統合システムの場合は、ポータルの URL は Azure Stack のデプロイのリージョン名と外部 FQDN (完全修飾ドメイン名) によって異なります。 管理ポータルの URL は、常に Azure Stack Development Kit (ASDK) デプロイと同じです。 

| 環境 | 管理者ポータルの URL |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| 統合システム | https://adminportal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> ASDK 環境の場合は、最初に、リモート デスクトップ接続または仮想プライベート ネットワーク (VPN) を使用して[開発キットのホストに接続](azure-stack-connect-azure-stack.md)できることを確認する必要があります。

 ![管理ポータル](media/azure-stack-manage-portals/admin-portal.png)

すべての Azure Stack デプロイの既定のタイム ゾーンは、協定世界時 (UTC) に設定されます。 

管理者ポータルでは、次のような操作を行うことができます。

* [Azure Stack を Azure に登録する](azure-stack-registration.md)
* [マーケットプレースを設定する](azure-stack-download-azure-marketplace-item.md)
* [ユーザーのためのプラン、オファー、およびサブスクリプションを作成する](azure-stack-plan-offer-quota-overview.md)
* [正常性とアラートの監視](azure-stack-monitor-health.md)
* [Azure Stack の更新プログラムを管理する](azure-stack-updates.md)

**[クイック スタートのチュートリアル]** タイルに、最も一般的なタスクのオンライン ドキュメントへのリンクがあります。

オペレーターは、管理ポータルで仮想マシン、仮想ネットワーク、ストレージ アカウントなどのリソースを作成することができますが、リソースを作成してテストするためには、[ユーザー ポータルにサインイン](user/azure-stack-use-portal.md)する必要があります。

>[!NOTE]
>管理ポータルの [クイック スタートのチュートリアル] タイルにある **[仮想マシンの作成]** リンクから仮想マシンを作成できますが、これは Azure Stack が正常にデプロイされたことを検証するときにのみ使用します。

## <a name="understand-subscription-behavior"></a>サブスクリプション操作の概要

管理ポータルでは、消費、既定プロバイダー、および測定という 3 つのサブスクリプションが既定で作成されます。 オペレーターは、主に*既定プロバイダー サブスクリプション*を使用します。 他のサブスクリプションを追加して管理ポータルで使用することはできません。 

その他のサブスクリプションは、管理者がユーザー用に作成するプランとオファーに基づいて、ユーザー ポータルでユーザーによって作成されます。 ただし、ユーザー ポータルでは、管理ポータルの管理機能にも運用機能にもアクセスできません。

管理ポータルとユーザー ポータルは、Azure Resource Manager の個別のインスタンスによってサポートされます。 この Azure Resource Manager による分離のため、ポータルをまたいでサブスクリプションを使用することはできません。 たとえば、Azure Stack オペレーターとしてユーザー ポータルにサインインした場合は、*既定プロバイダー サブスクリプション*にアクセスすることはできません。 管理機能にアクセスすることはできませんが、利用できる公開オファーから自分でサブスクリプションを作成することはできます。 ユーザー ポータルにサインインしている限り、テナント ユーザーと見なされます。

  >[!NOTE]
  >ASDK 環境では、ユーザーが Azure Stack オペレーターと同じテナント ディレクトリに属している場合、ユーザーは管理ポータルへのサインインをブロックされません。 ただし、ユーザーは管理機能にはアクセスできず、サブスクリプションを追加して、ユーザー ポータルで利用できるオファーにアクセスすることもできません。

## <a name="administration-portal-tips"></a>管理ポータルに関するヒント

### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ

ダッシュボードには、既定のタイルのセットが含まれています。 **[ダッシュボードの編集]** を選択して既定のダッシュボードを変更したり、**[新しいダッシュボード]** を選択してカスタムのダッシュボードを追加したりできます。 ダッシュボードにはタイルを簡単に追加できます。 たとえば、**[+ リソースの作成]** を選択し、**[オファー + プラン]** を右クリックし、**[ダッシュボードにピン留めする]** を選択します。

ポータルに空のダッシュボードが表示されることがあります。 ダッシュボードを回復するには、**[ダッシュボードの編集]** をクリックし、右クリックして、**[既定の状態にリセット]** を選択します。

### <a name="quick-access-to-online-documentation"></a>オンライン ドキュメントへのクイック アクセス

Azure Stack オペレーター向けドキュメントにアクセスするには、管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) を使用します。 カーソルをアイコンに移動し、**[ヘルプとサポート]** を選択します。

### <a name="quick-access-to-help-and-support"></a>ヘルプとサポートへのクイック アクセス

管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) を選択し、**[新しいサポート要求]** を選択すると、次のいずれかの結果になります。

- 統合システムを使用している場合は、Microsoft カスタマー サポート サービス (CSS) のサポート チケットを直接開くことができるサイトが開きます。 Microsoft サポートと OEM (Original Equipment Manufacturer) ハードウェア ベンダーのサポートのどちらに連絡するかについては、「[サポートが受けられる場所](azure-stack-manage-basics.md#where-to-get-support)」を参照してください。
- ASDK を使用している場合は、[Azure Stack フォーラムのサイト](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack)が直接開かれます。 これらのフォーラムは定期的にチェックされています。 ASDK は評価環境であるため、Microsoft CSS を通した正式なサポートは提供されていません。

### <a name="quick-access-to-the-azure-roadmap"></a>Azure ロードマップへのクイック アクセス

管理ポータルの右上隅にある**ヘルプとサポート** (疑問符) を選択し、**[Azure ロードマップ]** を選択すると、新しいブラウザー タブが開き、Azure ロードマップが表示されます。 **[製品]** 検索ボックスに **Azure Stack** と入力すると、すべての Azure Stack ロードマップの更新プログラムを確認できます。

## <a name="next-steps"></a>次の手順

[Azure Stack を Azure に登録](azure-stack-registration.md)し、ユーザーに提供する項目を [Azure Stack マーケットプレース](azure-stack-marketplace.md)に設定します。 
