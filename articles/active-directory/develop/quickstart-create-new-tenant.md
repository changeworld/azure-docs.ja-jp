---
title: Azure AD テナントを取得する方法 | Microsoft Docs
description: アプリケーションを登録および構築するために Azure Active Directory テナントを取得する方法を説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 1f866d3ee56b0c9a1e7a986d3ac951764b6a1cae
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507553"
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Azure Active Directory テナントを取得する方法

Azure Active directory (Azure AD) では、 [テナント](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) は組織を代表するものです。 これは、Azure AD サービスの専用インスタンスであり、組織が Azure、Microsoft Intune、Office 365 といった Microsoft クラウド サービスにサインアップするなど、Microsoft との関係が生じたときに提供されて所有するものです。 各 Azure AD テナントは、他の Azure AD テナントと区別され分離されています。 

テナントは、企業内のユーザーとユーザーに関する情報 (パスワード、ユーザー プロファイル データ、アクセス許可など) を保持しています。 また、グループ、アプリケーション、および組織とそのセキュリティに関するその他の情報も含まれます。

Azure AD ユーザーがアプリケーションにサインインできるようにするには、自分のテナントにアプリケーションを登録する必要があります。 Azure AD テナントの作成とそのテナントでのアプリケーションの発行は、**完全に無料**です (ただし、料金を支払ってテナントでプレミアム機能を使用することもできます)。 実際、多くの開発者は、実験、開発、ステージング、テストのために複数のテナントとアプリケーションを作成します。

## <a name="use-an-existing-azure-ad-tenant"></a>既存の Azure AD テナントの使用

多くの開発者は、Azure AD テナントに関連付けられたサービスまたはサブスクリプション (Office 365 や Azure サブスクリプションなど) を通じてテナントを既に持っています。 既にテナントがあるかどうかをチェックするには、アプリケーションの管理に使用するアカウントで [Azure Portal](https://portal.azure.com) にサインインしてから、アカウント情報が表示される右上隅をチェックします。 テナントがある場合、自動的にそのテナントへのログインが行われます。テナント名はアカウント名のすぐ下に表示されます。 Azure portal の右上のアカウント名をポイントすると、名前、電子メール、ディレクトリ、テナント ID (GUID)、ドメインが表示されます。 アカウントが複数のテナントに関連付けられている場合は、アカウント名を選択してメニューを開き、そこでテナントを切り替えることができます。 各テナントには独自の ID があります。

> [!TIP]
> テナント ID を確認する必要がある場合、この情報を見つける方法が複数あります。 アカウント名をポイントすると、テナント ID を表示できます。また、Azure portal 上で **[Azure Active Directory]、[プロパティ]、[ディレクトリ ID]** の順に選択することもできます。

アカウントに関連付けられた既存のテナントがない場合は、アカウント名の下に GUID が表示されます。[新しいテナントを作成する](#create-a-new-azure-ad-tenant)まで、アプリの登録などのアクションは実行できません。

## <a name="create-a-new-azure-ad-tenant"></a>新しい Azure AD テナントの作成

Azure AD テナントがない場合、または新しいテナントを作成したい場合、[Azure Portal](https://portal.azure.com) の[ディレクトリ作成エクスペリエンス](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)を使用して作成できます。 このプロセスには約 1 分かかります。また、最後に新しく作成したテナントに移動するよう求められます。
