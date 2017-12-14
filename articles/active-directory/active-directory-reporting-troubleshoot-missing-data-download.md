---
title: "トラブルシューティング: ダウンロードした Azure Active Directory アクティビティ ログにデータが見つからない | Microsoft Docs"
description: "ダウンロードした Azure Active Directory アクティビティ ログにデータが見つからない問題の解決策を提供します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9fc5f8f3325f061e67fef053437963b167f4073b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>ダウンロードした Azure Active Directory アクティビティ ログにデータが見つかりません


## <a name="symptoms"></a>現象

アクティビティ ログ (監査またはサインイン) をダウンロードしましたが、選択した期間のレコードがまったく表示されません。 なぜですか? 

 ![レポート](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>原因

Azure Portal でアクティビティ ログをダウンロードする場合は、新しい順に並べ替えられた最新の 12 万件のレコードに制限されます。 

## <a name="resolution"></a>解決策

[Azure AD Reporting API](active-directory-reporting-api-getting-started.md) を利用すると、任意の時点のレコードを最大 100 万件取得できます。 Reporting API を呼び出して、増分方式で一定期間 (たとえば、毎日や毎週) のレコードを取得するスクリプトを定期的に実行することをお勧めします。

## <a name="next-steps"></a>次のステップ
「[Azure Active Directory レポートに関する FAQ](active-directory-reporting-faq.md)」を参照してください。

