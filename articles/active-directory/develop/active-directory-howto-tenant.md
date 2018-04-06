---
title: Azure AD テナントを取得する方法 | Microsoft Docs
description: アプリケーションを登録および構築するために Azure Active Directory テナントを取得する方法を説明します。
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: ab7db49fa07f260de6ebbe4b2cee943b64cab7fe
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Azure Active Directory テナントを取得する方法
Azure Active directory (Azure AD) では、 [テナント](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) は組織を代表するものです。  これは、Azure AD サービスの専用インスタンスであり、組織が Azure、Microsoft Intune、Office 365 といった Microsoft クラウド サービスにサインアップするなど、Microsoft との関係が生じたときに提供されて所有するものです。  各 Azure AD テナントは、他の Azure AD テナントと区別され分離されています。  

テナントは、企業内のユーザーおよびパスワード、ユーザー プロファイル データ、アクセス許可などのユーザーに関する情報を保持しています。  また、グループ、アプリケーション、および組織とそのセキュリティに関するその他の情報も含まれます。

Azure AD ユーザーがアプリケーションにサインインできるようにするには、自分のテナントにアプリケーションを登録する必要があります。  Azure AD テナントの作成とそのテナントでのアプリケーションの発行は、**完全に無料**です (ただし、料金を支払ってテナントでプレミアム機能を使用することもできます)。  実際、多くの開発者は、実験、開発、ステージング、テストのために複数のテナントとアプリケーションを作成します。

## <a name="use-an-existing-azure-ad-tenant"></a>既存の Azure AD テナントの使用

多くの開発者は、Azure AD テナントに関連付けられたサービスまたはサブスクリプションを通じてテナントを既に持っています (例: Office 365 や Azure サブスクリプション)。  既にテナントがあるかどうかをチェックするには、アプリケーションの管理に使用するアカウントで [Azure Portal](https://portal.azure.com) にサインインしてから、アカウント情報が表示される右上隅をチェックします。  テナントがある場合、自動的にそのテナントへのログインが行われます。テナント名はアカウント名のすぐ下に表示されます。  アカウントが複数のテナントに関連付けられている場合、アカウント名をクリックしてメニューを開き、そこでテナントを切り替えることができます。

アカウントに関連付けられた既存のテナントがない場合、アカウント名の下に GUID が表示されます。また、[新しいテナントを作成する](#create-a-new-azure-ad-tenant)まで、アプリの登録などのアクションを実行できません。

## <a name="create-a-new-azure-ad-tenant"></a>新しい Azure AD テナントの作成

Azure AD テナントがない場合、または新しいテナントを作成したい場合、[Azure Portal](https://portal.azure.com) の[ディレクトリ作成エクスペリエンス](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)を使用して作成できます。  このプロセスには約 1 分かかります。また、最後に新しく作成したテナントに移動するよう求められます。