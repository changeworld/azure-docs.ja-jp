---
title: Microsoft 顧客契約への署名後に発生したサブスクリプション アクセスの問題をトラブルシューティングする - Azure
description: この記事は、新しい Microsoft 顧客契約への署名後にサブスクリプション アクセスの問題が発生した場合のトラブルシューティングに役立ちます。
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: microsoft-customer-agreement
ms.topic: troubleshooting
ms.date: 04/07/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dff361bb3413bfc57383a284e5c162c61e7d6212
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724460"
---
# <a name="troubleshoot-subscription-access-after-you-sign-a-microsoft-customer-agreement"></a>Microsoft 顧客契約への署名後に発生したサブスクリプション アクセスの問題をトラブルシューティングする

この記事は、新しい Microsoft 顧客契約への署名後にサブスクリプション アクセスの問題が発生した場合のトラブルシューティングに役立ちます。 一般的な問題のトラブルシューティングと解決に、以下の情報をご利用ください。

## <a name="troubleshoot-subscription-access"></a>サブスクリプション アクセスのトラブルシューティング

課金アカウントの種類が Microsoft 顧客契約であることを確認します。 課金アカウントの種類は、Azure portal の **[コストの管理と請求]** で確認できます。 詳細については、[MCA にアクセスできるかどうかの確認](../understand/mca-understand-your-usage.md#check-access-to-a-microsoft-customer-agreement)に関するセクションを参照してください。

## <a name="troubleshoot-viewing-your-billing-account"></a>課金アカウントの表示に関するトラブルシューティング

課金アカウントの表示に問題があり、なおかつ複数のテナントをご利用の場合は、Azure portal でディレクトリの切り替えを試みてください。

1. Azure portal の右上で、自分の Azure アカウントを選択します。
1. [ディレクトリの切り替え] を選択します。  
    :::image type="content" source="./media/troubleshoot-subscription-access/switch-directory.png" alt-text="[ディレクトリの切り替え] オプションを示すスクリーンショット。" lightbox="./media/troubleshoot-subscription-access/switch-directory.png" :::
1. [ディレクトリ + サブスクリプション] ウィンドウで、他のディレクトリを選択して切り替えます。  
    :::image type="content" source="./media/troubleshoot-subscription-access/select-directory.png" alt-text="他のディレクトリの選択画面を示すスクリーンショット。" lightbox="./media/troubleshoot-subscription-access/select-directory.png" :::

## <a name="troubleshoot-account-access"></a>アカウントアクセスのトラブルシューティング

同じメール アドレスを使用する Microsoft アカウントが複数ある可能性があります。 個人用アカウントと職場アカウントをご利用ではないでしょうか。 Microsoft 顧客契約では、職場または学校アカウントが使用されます。 詳細については、[職場または学校アカウントでサインインする](https://support.microsoft.com/office/which-account-do-you-want-to-use-2b5bbd7a-7df6-4283-beff-8015e28eb7b9)方法に関するページを参照してください。

- 職場または学校アカウントでサインインします。  
    :::image type="content" source="./media/troubleshoot-subscription-access/two-accounts.png" alt-text="職場または学校アカウントの選択を示すスクリーンショット。" lightbox="./media/troubleshoot-subscription-access/two-accounts.png" :::

## <a name="next-steps"></a>次の手順

- [Microsoft 顧客契約のドキュメント](./index.yml)をお読みください。