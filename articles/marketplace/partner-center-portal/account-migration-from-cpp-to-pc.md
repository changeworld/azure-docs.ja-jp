---
title: Cloud パートナー ポータルからパートナー センターへのアカウントの移行 - Azure 用商業マーケットプレース
description: アカウントを CPP からパートナー センターに移行する方法。 - Azure 用 Commercial Marketplace
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c7680896507cd3738fa3bce0d30a516d08509c4
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383287"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Cloud パートナー ポータルからパートナー センターへのアカウントの移行

Cloud パートナー ポータル (CPP) からパートナー センター (PC) にオファーが移行されるときに、それらは CPP での編集用にロックされます。 この時点で、アカウントの設定をパートナー センターに移行する必要があります。 両方のアカウント設定とオファーをパートナー センターで管理できます。

## <a name="account-migration-process"></a>アカウントの移行プロセス

CPP からオファーが移行されるときに、アカウントが移行用に構成されます。 
 
CPP で所有者ロールを持っているユーザーの場合は、[発行元プロファイル] ページに黄色のバナーが表示されます。 アカウントの設定をパートナー センターに移動することを求められます。 

アカウント移行プロセスを開始するには、バナーをクリックします。 次の項目を入力することが期待されています。

### <a name="work-email-address"></a>仕事用メール アドレス

ほとんどの場合、CPP にサインインするために使用している電子メール アドレスでサインインします。 場合によっては、別の電子メール アドレスを使用する必要があります。

* Microsoft アカウント:CPP アカウントが Microsoft アカウントの場合は、MPN ID が登録され、テナントに関連付けられている有効な職場の電子メール アドレスを入力する必要があります。

* テナントの不一致:職場の電子メール アドレスが、CPP アカウント上に存在する Microsoft Partner Network ID に関連付けられているテナントに属していない場合は、エラーが表示されます。 このエラーを解消するには、テナントに関連付けられている電子メール アドレスを入力します。 エラー メッセージに、テナントの名前が示されます。

### <a name="sign-up-for-microsoft-partner-network-program"></a>Microsoft Partner Network プログラムへのサインアップ

CPP アカウントに Microsoft Partner Network ID がないか、あっても無効な場合は、アクティブ化プロセスの一環として、Microsoft Partner Network プログラムにサインアップする必要があります。

## <a name="after-account-migration-is-complete"></a>アカウントの移行の完了後

移行は、特定のアカウントに対して 1 回だけ行う必要があります。 特定のパートナーでアカウントの移行が完了すると、[発行元プロファイル] ページですべての所有者がこの動作を確認できます。

1. Microsoft Partner Network の [パートナーの設定] ページを表示して、アカウント設定を管理できます。 
2. CPP の [発行元プロファイル] ページの黄色のバナーは所有者ロールを持つユーザーに表示され、パートナー センターでアカウント設定を管理するようにそのユーザーに依頼します。
3. CPP の [アカウントの設定] ページは読み取り専用モードに変換されます。

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Dynamics 365 ベースのソリューションをパートナー センターに移動する

One Commercial Partner GTM ポータルで Dynamics 365 for Customer Engagement または Dynamics 365 for Finance and Operations ソリューションを作成した場合、**これらのソリューションはパートナー センターで管理する必要があります**。

**2019 年 8 月 31 日までにソリューションを移動しなかった場合は**、できるだけ早く以下の手順を完了してください。 それまでは、以下の状態になります。
- ISV はマーケティングの特典にアクセスできない
- 共同販売優先のステータスを失う
- Cloud Embed を必要とする場合、2019 年 10 月 15 日以降、準拠性を失う

> [!NOTE]
> MPN メンバーシップ アカウントが最初にパートナー メンバーシップ センター (PMC) で作成された場合は、下の手順を完了する前に、[パートナー センター](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)にサインインしてそのアカウントが移行されていることを確認してください。 MPN ID を含むプロファイル画面が表示されている場合は、処理を続行する準備ができています。 そうでない場合は、[パートナー メンバーシップ センター](https://partners.microsoft.com/partnerprogram/Welcome.aspx)のプロンプトに従って、アカウントの移行を開始する必要があります。 これに関するサポートが必要な場合は、[サポート](https://partner.microsoft.com/support?issueid=100-0077)にアクセスしてください。

1. [パートナー センターの商業マーケットプレースの概要ページ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)に移動します。 左側のナビゲーション ウィンドウに "商業マーケットプレース" が表示されている場合は、登録済みであり、次の手順に進んでください。 そうでない場合は、ここで[商業マーケットプレースに登録](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)します。
2. [プランを検索](https://appsource.microsoft.com/)して、プランが AppSource にあることを確認します。 プランが既に AppSource にある場合は、次の手順に進みます。 AppSource にないプランの場合は、[新しい Dynamics 365 for Customer Engagement プラン](create-new-customer-engagement-offer.md)または[新しい Dynamics 365 for Operations プラン](create-new-operations-offer.md)を作成します。
3. Business Applications ISV コネクト プログラムで登録を確認します。
  
   * パートナー センターの [[契約]](https://partner.microsoft.com/dashboard/account/agreements) ページで、プログラムに登録するために **Business Applications ISV 補遺**に同意したことを確認します。
   * [[アカウント設定]](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) ページで、課金情報を指定します。

4. プランが以前に認定されている場合でも、認定のために新規と既存の各プランを送信します。 資格がある場合は、このプロセス中に Premium レベルへの参加を要求することができます。 プランが以前に認定されている場合は、**2019 年 10 月 15 日までにアプリの再認定を完了する必要があります。** 認定または再認定を行うには、アプリが最新バージョンの Business Applications プラットフォームをサポートしている必要があります。
5. [One Commercial Partner GTM ポータル](https://msgtm.azurewebsites.net/en-US/Profile/SignIn)に移動し、[Marketplace Links] (マーケットプレースのリンク) セクションで AppSource の一覧の URL を追加します。 この手順に関するサポートが必要な場合は、cosell@microsoft.com に電子メールを送信してください。

## <a name="next-steps"></a>次の手順

- [パートナー センターで Commercial Marketplace アカウントを管理する](./manage-account.md) 
