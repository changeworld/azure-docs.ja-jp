---
title: リンクされたシングル サインオンをアプリケーションに追加する
description: Azure Active Directory で、リンクされたシングル サインオンをアプリケーションに追加します。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: bc50777e503c50ca68955b8324b75c7c6aa66c77
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548484"
---
# <a name="add-linked-single-sign-on-to-an-application"></a>リンクされたシングル サインオンをアプリケーションに追加する

この記事では、Azure Active Directory (Azure AD) でアプリケーションのリンクベースのシングル サインオン (SSO) を構成する方法について説明します。 Azure AD では、リンクベースの SSO を使用して、既に別のサービスの SSO が構成されているアプリケーションに SSO を提供できます。 [リンク] オプションを使用すると、ユーザーが組織のマイ アプリ、または Microsoft 365 ポータルでアプリケーションを選択するときにターゲットとなる場所を構成できます。

リンクベースの SSO では、Azure AD を使用したサインオン機能は提供されません。 このオプションでは、マイ アプリ、または Microsoft 365 ポータルでアプリケーションを選択したときにユーザーが送信される場所が設定されるだけです。

リンクベースの SSO が役立つ一般的なシナリオには、次のようなものがあります。
- Active Directory フェデレーション サービス (AD FS) など、現在フェデレーションを使用しているカスタム Web アプリケーションにリンクを追加する。
- ユーザーのアクセス ページに表示するだけの特定の Web ページにディープ リンクを追加する。
- 認証を必要としないアプリケーションにリンクを追加する。 リンクされたオプションでは、Azure AD 資格情報によるサインオン機能は提供されませんが、エンタープライズ アプリケーションの他の機能の一部は引き続き使用できます。 たとえば、監査ログを使用したり、カスタム ロゴとアプリケーション名を追加したりできます。

## <a name="prerequisites"></a>前提条件

Azure AD テナントでリンクベース SSO を構成するには、次のものが必要です。
-   アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
-   リンクベースの SSO をサポートするアプリケーション。

## <a name="configure-linked-based-single-sign-on"></a>リンクベースのシングル サインオンを構成する

1.  [Azure portal](https://portal.azure.com) に適切なロールでサインインします。
2.  Azure サービスで **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
3.  リンクされた SSO を追加するアプリケーションを検索して選択します。
4.  **[シングル サインオン]** を選択し、 **[リンク]** を選択します。
5.  アプリケーションのサインイン ページの URL を入力します。
6.  **[保存]** を選択します。 

## <a name="next-steps"></a>次のステップ

- [アプリへのアクセスを管理する](what-is-access-management.md)