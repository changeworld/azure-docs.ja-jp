---
title: Azure Active Directory とのディレクトリ同期
description: この同期パターンの実現に関するアーキテクチャ ガイダンス
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 862d17948cb09c18f9372f8b8b981e5efa6be71b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113957"
---
# <a name="directory-synchronization"></a>ディレクトリ同期

多くの組織は、オンプレミスとクラウドの両方のコンポーネントを包含するハイブリッド インフラストラクチャを備えています。 ローカル ディレクトリとクラウド ディレクトリの間でユーザーの ID を同期させると、ユーザーは、1 つの資格情報セットを使用してリソースにアクセスできるようになります。 

同期とは、次のプロセスのことです。 

* 特定の条件に基づいてオブジェクトを作成すること。
* オブジェクトを更新したままにすること。
* 条件が満たされなくなった場合にオブジェクトを削除すること。 

オンプレミスのプロビジョニングには、オンプレミス ソース (Active Directory など) から Azure Active Directory (Azure AD) へのプロビジョニングが含まれます。 

## <a name="use-when"></a>使用する状況

オンプレミスの Active Directory 環境から Azure AD に ID データを同期する必要がある場合です。

![アーキテクチャ図](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :Azure AD を使用して、アプリケーションにアクセスします。

* **Web ブラウザー** : アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。

* **アプリケーション** :認証と承認を目的とした Azure AD の使用に依存する Web アプリ。

* **Azure AD** :Azure AD Connect を経由して、組織のオンプレミス ディレクトリから ID 情報を同期します。 

* **Azure AD Connect** :オンプレミスの ID インフラストラクチャを Microsoft Azure AD に接続するためのツール。 ウィザード エクスペリエンスとガイド付きエクスペリエンスを使用すると、Active Directory から Azure AD への同期やサインオンなど、接続に必要な前提条件とコンポーネントを容易にデプロイおよび構成できます。 

* **Active Directory** : Active Directory は、ほとんどの Windows Server オペレーティング システムに含まれているディレクトリ サービスです。 Active Directory Domain Services (AD DS) が実行されているサーバーは、ドメイン コントローラーと呼ばれます。 これらによって、ドメイン内のすべてのユーザーとコンピューターが認証および承認されます。

## <a name="implement-directory-synchronization-with-azure-ad"></a>Azure AD とのディレクトリ同期を実装する

* [ID プロビジョニングとは](https://docs.microsoft.com/azure/active-directory/cloud-provisioning/what-is-provisioning) 

* [ハイブリッド ID ディレクトリ統合ツール](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Azure AD Connect インストールのロードマップ](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap)
