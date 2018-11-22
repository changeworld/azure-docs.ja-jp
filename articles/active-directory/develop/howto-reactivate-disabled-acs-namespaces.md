---
title: 無効化された Azure Access Control Service (ACS) 名前空間を再度有効化する方法
description: Azure Access Control Service (ACS) 名前空間を見つけて有効化し、2019 年 2 月 4 日まで有効化状態の延長を要請する方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 0f0de122dc3dbd770e91a8412430423bee222b30
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577961"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>方法: 無効化された Access Control Service 名前空間を再度有効化する

2017 年 11 月に、Azure Active Directory (Azure AD) のサービスである Microsoft Azure Access Control (ACS) が 2018 年 11 月 7 日で終了となると告知しました。

その後、ACS サブスクリプションの管理メールに ACS サービス終了に関する電子メールを送信しています。2018 年 11 月 7 日のサービス終了まで残り 12 か月、9 か月、6 か月、3 か月、1 か月、2 週間、1 週間、1 日のタイミングで送信されます。

2018 年 10 月 3 日に、2018 年 11 月 7 日までの移行を完了できないお客様に延長サービスを告知しました (電子メールと[ブログ投稿](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)で)。 この告知には、延長の要請方法も含まれていました。

## <a name="why-your-namespace-is-disabled"></a>名前空間が無効となっている理由

延長を選択していない場合、2018 年 11 月 7 日より ACS 名前空間の無効化を開始します。 連絡できなかったお客様が 2019 年 2 月 4 日までの延長を希望する場合、次のセクションの指示に従ってください。

> [!NOTE]
> サブスクリプションのサービス管理者または共同管理者でなければ、PowerShell コマンドを実行したり、延長を要求したりできません。

## <a name="find-and-enable-your-acs-namespaces"></a>自分の ACS 名前空間を見つけ、有効にする

ACS PowerShell を使用して自分の ACS 名前空間をすべて一覧表示し、無効になっている名前空間を再度有効にできます。

1. ACS PowerShell をダウンロードしてインストールする:
    1. PowerShell ギャラリーに移動し、[Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2) をダウンロードします。
    1. モジュールをインストールする:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. 使用できるすべてのコマンドの一覧を取得する:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        特定のコマンドのヘルプを表示するには、次を実行します。

        ```
        Get-Help [Command-Name] -Full
        ```
    
        `[Command-Name]` は、ACS コマンドの名前です。
1. **Connect-AcsAccount** コマンドレットを使用して ACS に接続します。 

    コマンドを実行する前に、**Set-ExecutionPolicy** を実行して実行ポリシーを変更することが必要な場合があります。
1. **Get-AcsSubscription** コマンドレットを使用して、使用可能な Azure サブスクリプションを一覧表示します。
1. **Get-AcsNamespace** コマンドレットを使用して、ACS 名前空間を一覧表示します。
1. `State` が `Disabled` であることを確認することで名前空間が無効になっていることを確認します。

    [![名前空間が無効になっていることを確認する](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    `nslookup {your-namespace}.accesscontrol.windows.net` を使用し、ドメインが依然として有効であるかどうかを確認することもできます。

1. **Enable-AcsNamespace** コマンドレットを使用し、ACS 名前空間を有効にします。

    名前空間を有効にしたら、2019 年 2 月 4 日の前にもう一度名前空間が無効にならないように延長を要請できます。 この日付を過ぎると、ACS に対するすべての要求が失敗します。

## <a name="request-an-extension"></a>延長を要請する

1. `https://{your-namespace}.accesscontrol.windows.net` に進み、自分の ACS 名前空間の管理ポータルに移動します。
1. **[使用条件を読む]** ボタンを選択し、[更新された利用規約](https://azure.microsoft.com/support/legal/access-control/)を読みます。更新された利用規約が含まれたページに自動的に移動します。

    [![[使用条件を読む] ボタンを選択する](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. ページの一番上にあるバナーの **[延長をリクエスト]** を選択します。 このボタンは[更新された利用規約](https://azure.microsoft.com/support/legal/access-control/)を読まないと有効になりません。

    [![[延長をリクエスト] ボタンを選択する](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. 延長依頼が登録されると、ページが更新され、ページの一番上に新しいバナーが表示されます。

    [![更新後のページとバナー](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>ヘルプとサポート

- この方法を試して問題が発生した場合、[Azure サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)までお問い合わせください。
- ACS のサービス終了についてご質問やご意見がございましたら、Microsoft までお問い合わせください (acsfeedback@microsoft.com)。

## <a name="next-steps"></a>次の手順

- 「[方法: Azure Access Control Service からの移行](active-directory-acs-migration.md)」で ACS サービス終了に関する情報を確認します。
