---
title: ISV アプリのライセンスの管理 - Microsoft AppSource と Azure Marketplace
description: Microsoft による ISV アプリのライセンス管理について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.date: 04/30/2021
ms.openlocfilehash: 7d91cacf68cca5dcff377cf151a004d05b4c3fbc
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111521"
---
# <a name="isv-app-license-management"></a>ISV アプリのライセンス管理

次のオファーの種類に適用されます。

- Dynamics 365 for Customer Engagement & Power Apps

_ISV アプリのライセンス管理_ を利用すると、Dynamics 365 製品スイートを使用してソリューションを構築する独立系ソフトウェア ベンダー (ISV) は、Microsoft が提供するシステムを使用して、構築したソリューションのライセンスを管理し、適用することができます。 このアプローチの採用により、次のことが可能になります。

- ソリューションの顧客は、Office や Dynamics のライセンス管理に使用する Microsoft 365 管理センターなどの使い慣れたツールを使用して、ソリューションのライセンスの割り当てや割り当て解除を実行できます。
- ライセンスされたユーザーのみがソリューションにアクセスできるよう、Power Platform によって実行時にライセンスが適用されるようにします。
- ライセンスの管理と適用のための独自システムを構築し、保守する手間が不要になります。


> [!NOTE]
> ISV アプリのライセンス管理は、ISV コネクト プログラムに参加している ISV のみが利用できます。 Microsoft はライセンスの販売には関与していません。

## <a name="prerequisites"></a>前提条件

ISV アプリのライセンスを管理するには、次の前提条件に従う必要があります。

1. 有効な [Microsoft Partner Network アカウント](/partner-center/mpn-create-a-partner-center-account)がある。
1. コマーシャル マーケットプレース プログラムに登録している。 詳細については、「[商業マーケットプレース アカウントをパートナー センターに作成する](create-account.md)」を参照してください。
1. [ISV コネクト プログラム](https://partner.microsoft.com/solutions/business-applications/isv-overview)に登録している。 詳細については、「[Microsoft Business Applications 独立系ソフトウェア ベンダー (ISV) コネクト プログラム オンボード ガイド](business-applications-isv-program.md)」を参照してください。
1. 開発者チームには、Dataverse ソリューションを作成するために必要な開発環境およびツールがあります。 Dataverse ソリューションには、モデル駆動型アプリケーションが含まれている必要があります (これらは現在、ライセンス管理機能を通じてサポートされている唯一の種類のソリューション コンポーネントです)。

## <a name="high-level-process"></a>プロセスの概要

この表は、ISV アプリのライセンスを管理するプロセスの概要です。

| 手順 | 詳細 |
| ------------ | ------------- |
| 手順 1: オファーを作成する | ISV がパートナー センターでオファーを作成し、Microsoft 経由でこのオファーのライセンスを管理することを選択します。 これには、オファーのライセンス プランを 1 つ以上定義することが含まれます。 詳しくは、「[Microsoft AppSource での Dynamics 365 for Customer Engagement & Power Apps オファーの作成](dynamics-365-customer-engage-offer-setup.md)」を参照してください。 |
| 手順 2: パッケージを更新する | ISV は、ライセンス プランの情報をメタデータとして含めたうえでオファーのソリューション パッケージを作成し、パートナー センターにアップロードして Microsoft AppSource に公開します。 詳細については、[ソリューションへのライセンス メタデータの追加](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution)に関するページを参照してください。 |
| 手順 3: ライセンスを購入する | 顧客は ISV のオファーを AppSource で探すか、ISV の Web サイトで直接探します。 顧客は、必要なプランのライセンスを ISV から直接購入します (現時点では、これらのオファーは AppSource 経由での購入はできません)。 |
| 手順 4: 取引を登録する | ISV は、パートナー センターで購入を Microsoft に登録します。 [取引登録](/partner-center/csp-commercial-marketplace-licensing#register-isv-connect-deal-in-deal-registration)の過程で、ISV は、顧客が購入した各ライセンス プランの種類と数量を指定します。 |
| 手順 5: ライセンスを管理する | Microsoft 365 管理センターに表示されたライセンス プランを、顧客が組織内の[ユーザーまたはグループに割り当て](/microsoft-365/commerce/licenses/manage-third-party-app-licenses)ます。 顧客は Power Platform 管理センターを使用してアプリケーションをテナントにインストールすることもできます。 |
| 手順 6: ライセンス チェックを実行する | 顧客の組織内のユーザーがアプリケーションを実行しようとすると、実行が許可される前に、ユーザーがライセンスを持っていることが Microsoft によって確認されます。 ライセンスを持っていない場合、ライセンスの管理者に問い合わせる必要があるというメッセージがユーザーに表示されます。 |
| 手順 7: レポートを表示する | ISV は、プロビジョニングされて割り当てられたライセンスに関する情報を、期間または地域を指定して表示できます。 |
|||

## <a name="enabling-app-license-management-through-microsoft"></a>Microsoft によるアプリ ライセンス管理の有効化

オファーの作成時に、[オファー設定] タブにある 2 つのチェック ボックスを使用して、オファーの ISV アプリ ライセンス管理を有効にします。

### <a name="enable-app-license-management-through-microsoft-check-box"></a>[Microsoft によるアプリのライセンス管理を有効にする] チェック ボックス

しくみは次のとおりです。

- **[Microsoft によるアプリのライセンス管理を有効にする]** チェック ボックスをオンにした後、オファーのライセンス プランを定義できます。
- AppSource のオファー情報ページで、 **[今すぐ入手する]** ボタンが顧客に表示されます。 顧客はこのボタンをクリックして、アプリのライセンス購入の連絡を ISV に送信できます。

### <a name="allow-customers-to-install-my-app-even-if-licenses-are-not-assigned-check-box"></a>[ライセンスが割り当てられていない場合でもアプリのインストールを顧客に許可する] チェック ボックス

最初のチェック ボックスをオンにすると、 **[ライセンスが割り当てられていない場合でもアプリのインストールを顧客に許可する]** チェック ボックスが表示されます。 このオプションは、ソリューションの一部基本機能をすべてのユーザーに無料で提供し、プレミアム機能については料金を請求する "フリーミアム" ライセンス戦略を採用する場合に役立ちます。 逆に、製品のライセンスを現在所有しているテナントのみが AppSource から製品をダウンロードできるようにする場合は、このオプションを選択しないでください。

> [!NOTE]
> このオプションを選択する場合、ライセンスを必要としないようにソリューション パッケージを構成する必要があります。

しくみは次のとおりです。

- オファー情報ページで、 **[今すぐ入手する]** ボタンと **[連絡する]** ボタンがすべての AppSource ユーザーに表示され、オファーのダウンロードとインストールが許可されます。
- このオプションを選択しない場合、AppSource は **[今すぐ入手する]** ボタンを表示する前に、ユーザーのテナントがソリューションのライセンスを 1 つ以上持っていることを確認します。 ユーザーのテナントにライセンスがない場合、 **[連絡する]** ボタンのみが表示されます。

オファーの構成の詳細については、「[Dynamics 365 for Customer Engagement & PowerApps オファーを作成する方法](dynamics-365-customer-engage-offer-setup.md)」を参照してください。

## <a name="offer-listing-page-on-appsource"></a>AppSource のオファー情報ページ

オファーの公開後にユーザーに表示されるボタンは、選択したオプションによって異なります。 次のスクリーンショットは、 **[今すぐ入手する]** と **[連絡する]** のボタンがある AppSource のオファー情報ページを示しています。

:::image type="content" source="./media/third-party-license/f365.png" alt-text="AppSource のオファー情報ページのスクリーンショット。[今すぐ入手する] と [連絡する] のボタンが表示されている。":::

***図 1: Microsoft AppSource のオファー情報ページ***

## <a name="next-steps"></a>次のステップ

- [Dynamics 365 オファーの計画](marketplace-dynamics-365.md)
- [Dynamics 365 for Customer Engagement & PowerApps オファーを作成する方法](dynamics-365-customer-engage-offer-setup.md)
