---
title: Azure Virtual Desktop のユーザーごとのアクセス価格の登録 - Azure
description: Azure Virtual Desktop のユーザーごとのアクセス価格に登録する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 67e16d2cf0a9ddbc7b4447b22207c908be4a3ac9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036658"
---
# <a name="enroll-your-subscription-in-per-user-access-pricing"></a>ユーザーごとのアクセス価格でサブスクリプションを登録する

外部ユーザーがデプロイに接続するには、サブスクリプションをユーザーごとのアクセス価格に登録する必要があります。 ユーザーごとのアクセス価格により、指定および管理する ID を使用して、組織外のユーザーがサブスクリプション内のアプリとデスクトップにアクセスできます。 登録済みサブスクリプションは、Azure Virtual Desktop のリソースに接続する個別のユーザー数に基づいて毎月課金されます。

>[!NOTE]
>外部 "*ユーザー*" と外部 "*ID*" を混同しないでください。 現在、Azure Virtual Desktop では、ゲスト アカウントまたは企業間 (B2B) ID を含む外部 ID はサポートされていません。 Azure Virtual Desktop を使用して内部ユーザーまたは外部ユーザーにサービスを提供する場合でも、それらのユーザーの ID を自分で作成して管理する必要があります。 ユーザーごとのアクセス価格は、Azure Virtual Desktop でゲスト ユーザー アカウントを有効にする方法ではありません。 詳細については、「[ライセンスとユーザーごとのアクセス価格について](licensing.md)」を参照してください。

## <a name="how-to-enroll"></a>登録方法

Azure サブスクリプションをユーザーごとのアクセス価格に登録するには、次のようにします。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。

2. 検索バーに「**Azure Virtual Desktop**」と入力し、[サービス] にある **[Azure Virtual Desktop]** を見つけて選択します。

3. **[Azure Virtual Desktop]** の概要ページで、 **[Per-user access pricing]\(ユーザーごとのアクセス価格\)** を選択します。

4. サブスクリプションの一覧で、Azure Virtual Desktop のリソースをデプロイするサブスクリプションを選択します。

5. **[登録]** を選択します。

6. 製品の使用条件を確認し、 **[登録]** を選択して登録を開始します。 登録プロセスが完了するには、最大で 1 時間かかる場合があります。

7. 登録が完了したら、サブスクリプションの一覧の **[Per-user access pricing]\(ユーザーごとのアクセス価格\)** 列の値を確認して、"登録中" から "登録済み" に変更されたことを確認します。

## <a name="next-steps"></a>次のステップ

ユーザーごとのアクセス価格の詳細については、「[ライセンスとユーザーごとのアクセス価格について](licensing.md)」を参照してください。 デプロイのユーザーごとのアプリ ストリーミングのコストを見積もる方法については、「[Azure Virtual Desktop のユーザーごとのアプリ ストリーミング コストを見積もる](streaming-costs.md)」を参照してください。 合計デプロイ コストの見積もりについては、「[Azure Virtual Desktop の合計デプロイ コストについて](total-costs.md)」を参照してください。
