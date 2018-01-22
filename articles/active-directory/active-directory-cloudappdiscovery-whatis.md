---
title: "管理されていないクラウド アプリケーションを Azure Active Directory の Cloud App Discovery で検出する | Microsoft Docs"
description: "Cloud App Discovery でアプリケーションを検索および管理する利点と機能について説明します。"
services: active-directory
keywords: "Cloud App Discovery, アプリケーションの管理"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1d0ad06fc7eec07f8e1e0ba47121b6eec01c87df
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2018
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>管理されていないクラウド アプリケーションを Cloud App Discovery で検出する
## <a name="summary"></a>まとめ

現在、Azure Active Directory の Cloud App Discovery によって提供されるエージェントレス検出エクスペリエンスは、Microsoft Cloud App Security を利用して強化されています。 Cloud App Discovery を使用するには、Azure AD Premium P1 資格情報を使用してサインインします。 この更新プログラムは Azure AD Premium P1 のすべてのお客様に追加費用なしで提供されます。 まず、[Azure AD の Cloud App Discovery のセットアップ](https://docs.microsoft.com/azure/active-directory/cloudappdiscovery-get-started)に関する記事を読んでから、[Microsoft Cloud App Security](https://portal.cloudappsecurity.com/) を試用してください。

> [!IMPORTANT] 
> エージェントベースの検出機能を備えた現在の Azure AD Cloud App Discovery エクスペリエンスは 2018 年 3 月 5 日に終了します。その後、エージェントは無効になりデータは削除されます。 サービスの中断を回避するために、新しいエクスペリエンスを準備して実行できるように 3 月 5 日までに行動してください。  
 
**Cloud App Discovery を使用すると次のことができます。**

* 使用されているクラウド アプリケーションを検出し、ユーザー数、トラフィック量、アプリケーションに対する Web 要求数別に使用状況を測定する
* アプリケーションを使用しているユーザーを特定する
* オフライン分析用にデータをエクスポートする
* これらのアプリケーションを IT 部門の管理下に置き、ユーザー管理のためのシングル サインオンを有効にする

## <a name="how-it-works"></a>動作のしくみ
1. アプリケーションの使用状況に対するエージェントがユーザーのコンピューターにインストールされます。
2. エージェントによってキャプチャされるアプリケーションの使用状況情報は、セキュリティで保護され、暗号化されたチャネル経由で Cloud App Discovery サービスに送信されます。
3. Cloud App Discovery サービスでは、データが評価され、レポートが生成されます。

![Cloud App Discovery の図](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>次の手順
* [Cloud App Discovery のセキュリティとプライバシーの考慮事項](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery のカスタム ポートを使用するプロキシ サービス用レジストリ設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery エージェントの変更ログ ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

