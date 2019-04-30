---
title: Azure Active Directory レポートに Azure Monitor ブックを使用する | Microsoft Docs
description: Azure Active Directory レポートに Azure Monitor ブックを使用する方法を学習する
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013010"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>方法:Azure Active Directory レポートに Azure Monitor ブックを使用する

以下を行いたいですか?

- ユーザーのサインイン エクスペリエンスに与える[条件付きアクセス ポリシー](../conditional-access/overview.md)の影響を理解したい。

- サインイン エラーのトラブルシューティングを行って、問題をすばやく解決するだけでなく、組織のサインインの正常性をより理解したい。

- レガシ認証を使用して環境にアクセスしているユーザーを知りたい。 [レガシ認証をブロック](../conditional-access/block-legacy-authentication.md)することで、テナントの保護を向上させることができます。


[Azure Monitor ブック](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)は、テキスト、Analytics クエリ、Azure メトリック、パラメーターを組み合わせて内容豊富な対話型レポートを作成します。 Azure Active Directory は、上記の質問に対する回答を見つけるのに役立つ、監視のためのブックを提供します。

この記事の内容は次のとおりです。

- [Azure Monitor ブックを使用した対話型レポートの作成](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)方法に精通していることを前提としています。

- 上記の質問に回答するための監視について Azure Monitor ブックを使用する方法を説明します。
 


## <a name="prerequisites"></a>前提条件

この機能を使用するには、次が必要です。

- プレミアム (P1/P2) ライセンスがある Azure Active Directory テナント。 Premium ライセンスの取得方法については、[こちら](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)をご覧ください。

- [Log Analytics ワークスペース。](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

## <a name="access-workbooks"></a>ブックにアクセスする 

ブックにアクセスするには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

3. **[監視]** セクションで、**[洞察]** をクリックします。 

    ![洞察](./media/howto-use-azure-monitor-workbooks/41.png)

4. レポートまたはテンプレートをクリックするか、ツールバーで **[オープン]** をクリックします。 

    ![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>サインインの分析

サインインの分析ブックにアクセスするには、**[使用状況]** セクションで **[サインイン]** をクリックします。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success

- 保留中のユーザー アクション

- 失敗

各傾向は次でフィルター処理できます。

- 時間範囲

- アプリケーション

- ユーザー

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/43.png)


傾向ごとに次による内訳が得られます。

- Location

    ![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/45.png)

- Device

    ![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>レガシ認証を使用したサインイン 


[レガシ認証](../conditional-access/block-legacy-authentication.md)ブックを使用してサインインにアクセスするには、**[使用状況]** セクションで **[レガシ認証を使用したサインイン]** をクリックします。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success


各傾向は次でフィルター処理できます。

- 時間範囲

- アプリケーション

- ユーザー

- プロトコル 

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/47.png)


傾向ごとにアプリとプロトコル別の内訳が得られます。

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>条件付きアクセスでのサインイン 


[条件付きアクセス ポリシー](../conditional-access/overview.md) ブックによるサインインにアクセスするには、**[条件付きアクセス]** セクションで **[条件付きアクセスでのサインイン]** をクリックします。 

このブックには、無効化されたサインインの傾向が示されます。

各傾向は次でフィルター処理できます。

- 時間範囲

- アプリケーション

- ユーザー

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/49.png)


無効化されたサインインに対し、条件付きアクセスの状態別の内訳が得られます。

![条件付きアクセスの状態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>許可コントロールによるサインイン

[許可コントロール](../conditional-access/controls.md) ブックによるサインインにアクセスするには、**[条件付きアクセス]** セクションで **[許可コントロール別のサインイン]** をクリックします。 

このブックには次の無効化されたサインインの傾向が示されます。

- Require MFA (MFA が必須)
 
- 利用規約が必須

- プライバシーに関する声明が必須

- その他


各傾向は次でフィルター処理できます。

- 時間範囲

- アプリケーション

- ユーザー

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/50.png)


傾向ごとにアプリとプロトコル別の内訳が得られます。

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>サインイン エラー分析

**サインイン エラー分析**ブックを使用して、以下のエラーをトラブルシューティングします。

- サインイン
- 条件付きアクセス ポリシー
- レガシ認証 


データの条件付きアクセスによるサインインにアクセスするには、**[トラブルシューティング]** セクションで **[レガシ認証を使用したサインイン]** をクリックします。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success

- 保留中のアクション

- 失敗


各傾向は次でフィルター処理できます。

- 時間範囲

- アプリケーション

- ユーザー

![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/52.png)


サインインをトラブルシューティングするため、次による内訳を取得します。

- 主なエラー

    ![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/53.png)

- ユーザー アクションを待機しているサインイン

    ![[ギャラリー]](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>次の手順

* [Azure Monitor ブックを使用した対話型レポートの作成](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)