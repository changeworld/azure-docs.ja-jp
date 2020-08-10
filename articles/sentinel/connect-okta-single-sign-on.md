---
title: Azure Sentinel に Okta シングル サインオン データを接続する | Microsoft Docs
description: Azure Sentinel に Okta シングル サインオン データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 37ade037b7f3c88f5ff33d7fc4640b19f366fe7a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528207"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Azure 関数を使用して Okta シングル サインオンを Azure Sentinel に接続する

Okta シングル サインオン (SSO) コネクタを使用すると、Azure Sentinel にすべての [Okta シングル サインオン (SSO)](https://www.okta.com/products/single-sign-on/) セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Okta シングル サインオンと Azure Sentinel の統合では、Azure Functions を使用して REST API によりログ データを取得します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-okta-single-sign-on"></a>Okta シングル サインオンを構成して接続する

Azure Functions は、Okta シングル サインオンから直接イベントとログを統合およびプルし、Azure Sentinel に転送できます。

1. Azure Sentinel ポータルで、 **[データ コネクタ]** をクリックし、 **[Okta シングル サインオン]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **Okta シングル サインオン**ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の **Okta_CL** テーブルの下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Function Apps を使用して Okta シングル サインオンを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

