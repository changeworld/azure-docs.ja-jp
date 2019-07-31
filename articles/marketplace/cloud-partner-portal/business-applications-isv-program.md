---
title: Microsoft Business Applications 独立系ソフトウェア ベンダー (ISV) コネクト プログラム オンボード ガイド
description: Business Applications ISV コネクト プログラムにオンボードする方法を示します。
services: Azure Marketplace, Cloud Partner Portal
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/12/2019
ms.author: evansma
ms.openlocfilehash: 988dfd0337f8cedd992b55aa32e07a73f35d964d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877425"
---
# <a name="microsoft-business-applications-independent-software-vendor-isv-connect-program-onboarding-guide"></a>Microsoft Business Applications 独立系ソフトウェア ベンダー (ISV) コネクト プログラム オンボード ガイド

[Business Applications ISV コネクト プログラム](https://partner.microsoft.com/solutions/business-applications/isv-overview)の目的は、*Dynamics 365 Customer Engagement and PowerApps* (Dynamics CE オファー) または *Dynamics 365 Finance and Operations* (Dynamics Ops オファー) を使用して最新のクラウドベースの基幹業務 (LOB) ソリューションを構築する独立系ソフトウェアベンダー (ISV) の成長と全体的な成功を促進することです。 ここではそれに参加する方法を示します。

> [!IMPORTANT]
> *Dynamics 365 Customer Engagement*、*Dynamics 365 for Finance and Operations*、および *PowerApps* アプリケーションを使用する既存の ISV パートナーは、[Microsoft AppSource](https://appsource.microsoft.com) から既存のオファーが削除されないように、**2019 年 10 月 15 日**より前に、以下のすべてのオンボード手順を完了する必要があります。

Business Applications ISV コネクト プログラムのすべての[技術、マーケティング、販売に関する有効化ベネフィット](https://partner.microsoft.com/en-us/solutions/business-applications/isv-overview)に登録して利用するため、既存の ISV パートナー (*Dynamics CE* オファーと *Ops* オファーを使用) には以下の手順を完了することをお願いします。 

 - パートナー センターの商業マーケットプレース プログラムで[アカウントを設定します](#set-up-your-microsoft-partner-center-account)
 - Business Applications ISV コネクト プログラムで[参加するための使用条件に同意します](#sign-the-business-applications-isv-program-addendum)
 - [課金情報を指定します](#set-up-your-billing-information)
 - Microsoft AppSource で[アプリケーションを再認定します](#recertify-your-application)

> [!IMPORTANT]
> 既存のオファーを変更する前に、上記の手順をすべて完了しておく必要があります (それまでは、"*Cloud パートナー ポータル*" でオファーを編集できません)。

## <a name="set-up-your-microsoft-partner-center-account"></a>Microsoft パートナー センター アカウントを設定する

[パートナー センター](https://partner.microsoft.com)は、新しい Business Applications ISV コネクト プログラムのアカウント設定を管理する場所です。

> [!NOTE]
> このステップでは、特定のアカウントに対する Cloud パートナー ポータルでの "*所有者*" ロールが必要です。

Dynamics CE オファーまたは Dynamics Ops オファーを既に発行している場合は、通常、Cloud パートナー ポータル (CPP) アカウントはパートナー センターに既に移行されてアクティブ化されています。 そうではない場合は、CPP 発行元プロファイルからパートナー センター アカウントを手動でアクティブ化することができます。

1. [Cloud パートナー ポータル](https://cloudpartner.azure.com/)で、**発行元プロファイル**のページにサインインします。

2. アカウント設定がパートナー センターに移行済みであることを示すバナーが表示される場合は、次のセクションに進みます。 それ以外の場合は、パートナー センター アカウントのアクティブ化を求めるバナーを選択します。

詳しくは、「[*Cloud パートナー ポータルからパートナー センターへのアカウントの移行*](../partner-center-portal/account-migration-from-cpp-to-pc.md)」をご覧ください。

## <a name="sign-the-business-applications-isv-program-addendum"></a>Business Applications ISV プログラム補遺に署名する

このプログラムに参加するには、"*Business Applications 補遺*" を確認してそれに同意する必要があります。

> [!NOTE]
> このステップで法的契約に署名するには、パートナー センターでアカウントに対する "*所有者*" または "*マネージャー*" のロールが必要です。 前のステップで[パートナー センター アカウントをアクティブ化する](#set-up-your-microsoft-partner-center-account)ことによって Cloud パートナー ポータル アカウントの移行を完了した場合、アクセス許可もパートナー センターに移行されています。

1. https://partner.microsoft.com/dashboard/account/agreements に移動します (メッセージが表示されたらパートナー センターにサインインします)。

2. **[商業マーケットプレース]** という見出しの下で、**Microsoft Business Applications ISV 補遺**を見つけます。

3. バージョンのリンクを選択し、契約を表示します。

4. **[Accept Agreement]\(契約に同意する\)** を選択し、契約に同意して受け入れます。

## <a name="set-up-your-billing-information"></a>課金情報を設定する

次に、Business Applications ISV コネクト プログラムの課金情報を設定します。

> [!NOTE]
> このステップで課金情報を更新するには、パートナー センターでアカウントに対する "*所有者*" または "*マネージャー*" のロールが必要です。

1. [パートナー センター](https://partner.microsoft.com/dashboard)にサインインします。

2. *[設定]* (歯車) アイコンを選択し、 **[開発者向け設定]** を選択します。

3. *[アカウント]* で、 **[課金プロファイル]** を選択します。

4. 法人から設定された主要連絡先と課金情報を確認し、必要な更新を行います。

5. 必要に応じて、税 ID/VAT ID の設定を指定します。

6. **[保存]** を選択して変更を保存します。

課金プロファイルはいつでも更新できますが、"*会社名*" と請求先住所の "*国*" は後で変更することはできません。

## <a name="recertify-your-application"></a>アプリケーションを再認定する

上記の手順を完了すると、既存の Dynamics 365 CE オファーおよび Dynamics 365 Ops オファーを Cloud パートナー ポータルで編集できるようになります (ページの更新が必要になる場合があります)。 CPP で新しいオファーを作成することもできます。

Business Applications ISV コネクト プログラムに登録する最後のステップは、既存アプリケーションの再認定を受けることです。

1. [手順に従って](https://partner.microsoft.com/en-us/solutions/business-applications/isv-publish)、AppSource に対するアプリケーションの再認定を受けます。

2. [新しいオファーを作成して Cloud パートナー ポータルに発行](manage-offers/cpp-manage-offers.md)します。 これはまったく新しいオファーであり、既存のオファーに代わるものではありません。

## <a name="further-info"></a>詳細情報

アカウントに関する問い合わせは、アカウント マネージャーまたは [Microsoft パートナー サポート](https://partner.microsoft.com/support)に対して行ってください。 Business Applications ISV コネクト プログラムおよび Microsoft パートナー センターの一般情報については、以下をご覧ください。

#### <a name="business-applications-for-isvs-online-article"></a>ISV 向け Business Applications (オンライン記事)
https://partner.microsoft.com/en-us/solutions/business-applications/isv-overview

#### <a name="overview-of-the-new-program-for-business-applications-isvs-pdf"></a>Business Applications ISV 向けの新しいプログラムの概要 (PDF)
https://aka.ms/BizAppsISVProgram

#### <a name="business-applications-isv-connect-program-faq-pdf"></a>Business Applications ISV コネクト プログラムの FAQ (PDF)
https://assetsprod.microsoft.com/business-applications-partner-faq.pdf

#### <a name="upcoming-program-for-business-applications-isvs-blog-post"></a>Business Applications ISV 向けの今後のプログラム (ブログ投稿)
https://cloudblogs.microsoft.com/dynamics365/bdm/2019/04/17/upcoming-program-for-business-applications-isvs/

