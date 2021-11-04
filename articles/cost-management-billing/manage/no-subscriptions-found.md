---
title: "\"サブスクリプションが見つかりません\" エラー - Azure portal へのサインイン"
description: Azure portal サインイン中にサブスクリプションが見つからないエラーが発生する問題の解決策を提供します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/28/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a90285db3b95402581ba917cef0833e93da91f44
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425787"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal"></a>Azure portal の "サブスクリプションが見つかりません" サインイン エラー

[Azure portal](https://portal.azure.com/) にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されることがあります。 この記事では、この問題の解決策について説明します。

## <a name="symptom"></a>症状

[Azure portal](https://portal.azure.com/) にサインインしようとすると、「サブスクリプションが見つかりません」というエラー メッセージが表示されます。

## <a name="cause"></a>原因

この問題は、誤ったディレクトリで選択した場合、または、自分のアカウントに十分なアクセス許可がない場合に発生します。

## <a name="solution"></a>解決策

### <a name="scenario-error-message-is-received-in-the-azure-portal"></a>シナリオ: [Azure portal](https://portal.azure.com) でエラー メッセージが表示される

この問題を解決するには、次の手順に従います。

* 右上にある自分のアカウントをクリックして、適切な Azure ディレクトリが選択されていることを確認します。

  ![Azure ポータルの右上にあるディレクトリを選択する](./media/no-subscriptions-found/directory-switch.png)
* 適切な Azure ディレクトリが選択されていてもエラー メッセージが表示される場合は、[アカウントに所有者ロールを割り当て](../../role-based-access-control/role-assignments-portal.md)ます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
