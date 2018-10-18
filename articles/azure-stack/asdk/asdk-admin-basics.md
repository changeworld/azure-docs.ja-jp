---
title: Azure Stack Development Kit の基礎 | Microsoft Docs
description: Azure Stack Development Kit (ASDK) の基本的な管理作業を実行する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 555bf95367ab0d3e6c4afe09edb6777899400dd8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344670"
---
# <a name="asdk-administration-basics"></a>ASDK の管理の基本 
Azure Stack Development Kit (ASDK) の管理経験がない場合は、知っておく必要があることがいくつかあります。 このガイダンスでは、評価環境での Azure Stack オペレーターとしての役割の概要と、テスト ユーザーの生産性を迅速に向上させる方法を示します。

まず、「[What is Azure Stack Development Kit?](asdk-what-is.md)」(Azure Stack Development Kit とは) という記事に目を通し、ASDK の目的と制限事項について確実に理解しておく必要があります。 開発キットは "サンド ボックス" として使用する必要があります。その中では、Azure Stack を評価し、非運用環境でアプリの開発とテストを行うことができます。 

Azure と同様に、Azure Stack はイノベーションが迅速であるため、ASDK の新しいビルドを定期的にリリースします。 ただし、Azure Stack 統合システムのデプロイとは異なり、ASDK をアップグレードすることはできません。 そのため、最新のビルドに移行する場合は、[ASDK を再度デプロイする](asdk-redeploy.md)必要があります。 更新プログラム パッケージは適用できません。 このプロセスは時間がかかりますが、最新の機能が使用可能になったらすぐに試すことができるというメリットがあります。 

## <a name="what-account-should-i-use"></a>使用するアカウントについて
Azure Stack を管理する場合に注意するいくつかのアカウントに関する考慮事項があります。 特に、Azure Active Directory (Azure AD) ではなく ID プロバイダーとして Windows Server Active Directory Federation Services (AD FS) を使用する展開では注意が必要です。 次のアカウントに関する考慮事項は、Azure Stack 統合システムと ASDK 展開の両方に適用されます。

|Account|Azure AD|AD FS|
|-----|-----|-----|
|ローカル管理者 (.\Administrator)|ASDK ホスト管理者|ASDK ホスト管理者|
|AzureStack\AzureStackAdmin|ASDK ホスト管理者<br><br>Azure Stack 管理者ポータルにログインできます<br><br>Service Fabric リングを表示および管理するためのアクセス権|ASDK ホスト管理者<br><br>Azure Stack 管理者ポータルへのアクセス権なし<br><br>Service Fabric リングを表示および管理するためのアクセス権<br><br>既定のプロバイダー サブスクリプション (DPS) の所有者ではなくなりました|
|AzureStack\CloudAdmin|特権エンドポイント内で許可されたコマンドにアクセス、実行できます|特権エンドポイント内で許可されたコマンドにアクセス、実行できます<br><br>ASDK ホストにログインできません<br><br>既定のプロバイダー サブスクリプション (DPS) の所有者|
|Azure AD 全体管理者|インストール時に使用<br><br>既定のプロバイダー サブスクリプション (DPS) の所有者|適用不可|
|

## <a name="what-tools-do-i-use-to-manage"></a>管理に使用するツールについて
[Azure Stack 管理者ポータル](https://adminportal.local.azurestack.external)または PowerShell を使って Azure Stack を管理できます。 基本的概念を学ぶためには、ポータルを介するのが最も容易な方法です。 PowerShell を使う場合は、[PowerShell for Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) をインストールし、[GitHub から Azure Stack ツールをダウンロードする](asdk-post-deploy.md#download-the-azure-stack-tools)必要があります。

Azure Stack はデプロイ、管理、整理のための基礎となるメカニズムとして、Azure Resource Manager を使用します。 Azure Stack の管理とユーザーのサポートを行う予定であれば、Azure Resource Manager について学習する必要があります。 詳しくは、「[Azure Resource Manager の概要](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)」ホワイトペーパーをご覧ください。

## <a name="your-typical-responsibilities"></a>クラウド オペレーターの通常の担当範囲
ユーザーはサービスを使用する必要があります。 ユーザーの観点からは、クラウド オペレーターの主な役割は、ユーザーがこれらのサービスを使用できるようにすることです。 ASDK を使って、提供するサービスと、[プラン、オファー、クォータを作成する](asdk-offer-services.md)ことで、それらのサービスを使用できるようにする方法を確認できます。 仮想マシンのイメージなど、マーケットプレースに項目を追加する必要もあります。 Azure から Azure Stack に[マーケットプレースの項目をダウンロードする](asdk-marketplace-item.md)のが最も容易な方法です。

> [!NOTE]
> プラン、オファー、およびサービスをテストする場合は、[管理者ポータル](https://adminportal.local.azurestack.external)ではなく[ユーザー ポータル](https://portal.local.azurestack.external)を使用する必要があります。

サービスを提供することに加えて、Azure Stack オペレーターとして Azure を稼働させ続けるという通常の業務をすべて実行する必要があります。 これには以下のような業務が含まれます。
- Azure Active Directory (Azure AD) のデプロイまたは Active Directory フェデレーション サービス (AD FS) のデプロイのためのユーザー アカウントを追加する。
- ロールベースのアクセス制御 (RBAC) のロールを割り当てる (これは管理者に限られません)
- インフラストラクチャの正常性を監視する
- ネットワークとストレージのリソースを管理する
- 開発キットのホスト コンピューターで問題のあるハードウェアを交換する 

## <a name="where-to-get-support"></a>サポートが受けられる場所
開発キットのサポート オプションは、[Microsoft Azure Stack フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)でサポート関連の質問をすることだけです。 管理者ポータルの右上隅にあるヘルプとサポートのアイコン (疑問符) をクリックし、**[新しいサポート要求]** をクリックすると、フォーラムのサイトが直接開かれます。 これらのフォーラムは定期的にチェックされています。 

> [!IMPORTANT]
> ASDK は評価環境として提供されるものであるため、Microsoft カスタマー サポート サービス (CSS) を通じて公式なサポートは提供されません。

## <a name="next-steps"></a>次の手順
[ASDK をデプロイする](asdk-install.md)

