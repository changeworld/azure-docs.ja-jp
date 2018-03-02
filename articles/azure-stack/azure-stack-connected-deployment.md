---
title: "Azure Stack 統合システムの Azure に接続されたデプロイの決定 | Microsoft Docs"
description: "マルチノード Azure Stack の Azure に接続されたデプロイのためのデプロイ計画の決定を確認します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: def9d5381144026b5ad0e8a076edd3c0692a08f4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure Stack 統合システムの Azure に接続されたデプロイ計画の決定
[Azure Stack をハイブリッド クラウド環境に統合する方法](azure-stack-connection-models.md)を決定したら、Azure Stack デプロイの決定を完成させることができます。

Azure に接続された Azure Stack をデプロイすることは、ID ストアとして Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) のどちらかを使用できることを示します。 また、従量制または容量ベースのどちらかの課金モデルも選択できます。 接続されたデプロイは、特に Azure と Azure Stack の両方を含むハイブリッド クラウドのシナリオの場合、顧客が Azure Stack を最大限に活用できるため、既定のオプションです。 

## <a name="choose-an-identity-store"></a>ID ストアを選択する
接続されたデプロイでは、ID ストアとして Azure AD または AD FS のどちらかを選択できます。 インターネット接続のない切断されたデプロイは、AD FS しか使用できません。

ID ストアの選択は、テナント仮想マシン (VM) とは何の関係もありません。 テナント VM は、構成される方法に応じて接続先の ID ストアを選択できます (Azure AD、参加している Windows Server Active Directory ドメイン、ワークグループなど)。これは、Azure Stack ID プロバイダーの決定とは関係ありません。 

たとえば、Azure Stack の上に IaaS テナント VM をデプロイし、それを企業の Active Directory ドメインに参加させ、そこからアカウントを使用する場合は、引き続きこれを実行できます。 それらのアカウント用に、ここで選択した Azure AD ID ストアを使用する必要はありません。

### <a name="azure-ad-identity-store"></a>Azure AD ID ストア
ID ストアとして Azure AD を使用する場合は、グローバル管理者アカウントと課金アカウントの 2 つの Azure AD アカウントが必要です。 これらのアカウントは同じアカウントにすることも、別のアカウントにすることもできます。 Azure アカウントの数が制限されている場合は同じユーザー アカウントを使用する方が簡単で便利な可能性がありますが、ビジネス ニーズによって 2 つのアカウントの使用が指定されることもあります。

1. **グローバル管理者アカウント** (接続されたデプロイにのみ必要です)。 これは、Azure Active Directory 内の Azure Stack インフラストラクチャ サービスのためのアプリケーションとサービス プリンシパルを作成するために使用される Azure アカウントです。 このアカウントには、Azure Stack システムがデプロイされるディレクトリへのディレクトリ管理者権限が必要です。 これが Azure AD テナントの "クラウド オペレーター" グローバル管理者になり、次のように使用されます。 
    - Azure Active Directory および Graph API と対話する必要のあるすべての Azure Stack サービスのためのアプリケーションとサービス プリンシパルをプロビジョニングおよび委任するため。 
    - サービス管理者アカウントとして。 これは、既定のプロバイダー サブスクリプションの所有者 (後で変更可能) です。 このアカウントで Azure Stack 管理ポータルにログインできるほか、これを使用してオファーやプランを作成したり、クォータを設定したり、Azure Stack のその他の管理機能を実行したりできます。
2. **課金アカウント** (接続されたデプロイと切断されたデプロイの両方に必要です)。 この Azure アカウントは、Azure Stack 統合システムと Azure コマース バックエンドの課金関係を確立するために使用されます。 これは、Azure Stack の料金に対して課金されるアカウントです。 このアカウントはまた、マーケットプレース シンジケーションやその他のハイブリッド シナリオにも使用されます。 

### <a name="ad-fs-identity-store"></a>AD FS ID ストア
このオプションは、サービス管理者アカウント用に独自の ID ストア (企業の Active Directory など) を使用する場合に選択します。  

## <a name="choose-a-billing-model"></a>課金モデルを選択する
**従量制**または**容量**課金モデルのどちらかを選択できます。 従量制課金モデルのデプロイは 30 日ごとに少なくとも 1 回、接続を通して Azure に使用状況を報告できる必要があります。 したがって、従量制課金モデルは、接続されたデプロイでのみ使用できます。  

### <a name="pay-as-you-use"></a>従量制
従量制課金モデルでは、Azure サブスクリプションに対して使用状況が課金されます。 Azure Stack サービスを使用した場合にのみ支払います。 このモデルに決定した場合は、Azure サブスクリプションと、そのサブスクリプションに関連付けられたアカウント ID (serviceadmin@contoso.onmicrosoft.com など) が必要になります。 EA、CSP、および CSL サブスクリプションがサポートされています。 使用状況レポートは、[Azure Stack の登録](azure-stack-registration.md)中に構成されます。

> [!NOTE]
> ほとんどの場合、エンタープライズ顧客は EA サブスクリプションを使用し、サービス プロバイダーは CSP または CSL サブスクリプションを使用します。

CSP サブスクリプションを使用する場合は、厳密な CSP シナリオによって正しいアプローチが異なるため、下の表を確認してどの CSP サブスクリプションを使用するかを特定してください。

|シナリオ|ドメインおよびサブスクリプション オプション|
|-----|-----|
|ユーザーは**直接 CSP パートナー**または**間接 CSP プロバイダー**であり、Azure Stack を操作します|CSL (Common Service Layer) サブスクリプションを使用します。<br>     or<br>パートナー センターで、わかりやすい名前の Azure AD テナントを作成します  (例: &lt;組織>CSPAdmin とそれに関連付けられた Azure CSP サブスクリプション)。|
|ユーザーは**間接 CSP リセラー**であり、Azure Stack を操作します|パートナー センターを使用して、間接 CSP プロバイダーに、組織の Azure AD テナントとそれに関連付けられた Azure CSP サブスクリプションを作成するよう依頼します。|

### <a name="capacity-based-billing"></a>容量ベースの課金
容量課金モデルを使用することにした場合は、システムの容量に基づいて Azure Stack 容量プラン SKU を購入する必要があります。 正しい数量を購入するには、Azure Stack 内の物理コアの数がわかっている必要があります。 

容量課金では、登録のために Enterprise Agreement (EA) Azure サブスクリプションが必要です。 その理由は、登録によって Azure サブスクリプションを必要とするシンジケーションが設定されるためです。 このサブスクリプションは、Azure Stack の使用状況には使用されません。

## <a name="learn-more"></a>詳細情報
- ユース ケース、購入、パートナー、OEM ハードウェア ベンダーの詳細については、[Azure Stack](https://azure.microsoft.com/overview/azure-stack/) の製品ページを参照してください。
- Azure Stack 統合システムのロードマップと地理的な可用性については、ホワイト ペーパー「[Azure Stack: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)」 (Azure Stack: Azure の拡張機能) を参照してください。 
- Microsoft Azure Stack のパッケージと価格の詳細については、[.pdf をダウンロードしてください](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>次の手順
[データセンターのネットワーク統合](azure-stack-network.md)