---
title: Azure Active Directory との LDAP 同期
description: Azure Active Directory を使用した LDAP 同期の取得に関するアーキテクチャ ガイダンス。
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
ms.openlocfilehash: 61703654b11543f2c0f41fa68964cae287d940b8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128563751"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Azure Active Directory との LDAP 同期

ライトウェイト ディレクトリ アクセス プロトコル (LDAP : Lightweight Directory Access Protocol) は、TCP/IP スタックで実行されるディレクトリ サービス プロトコルです。 これは、インターネット ディレクトリへの接続、検索、および変更に使用される機構を提供します。 LDAP ディレクトリ サービスは、クライアント/サーバー モデルに基づき、その機能は既存のディレクトリにアクセスできるようにすることです。 多くの企業は、クリティカル ビジネス アプリのユーザーとグループを格納するために、オンプレミスの LDAP サーバーを利用しています。 

Azure Active Directory (Azure AD) によって、LDAP 同期を Azure AD Connect に置き換えることができます。 Azure AD Connect 同期サービスは、オンプレミスの環境と Azure AD の間における ID データの同期に関連するすべての操作を実行します。 

## <a name="use-when"></a>使用する状況

オンプレミスの LDAP v3 ディレクトリと Azure AD の間で ID データを同期する必要があります。 

![アーキテクチャの図](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :ユーザーとパスワードの並べ替えで DAP v3 ディレクトリの使用に依存するアプリケーションにアクセスします。

* **Web ブラウザー**:アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。

* **Web アプリ**:LDAP v3 ディレクトリに依存しているアプリケーションです。

* **Azure AD**: Azure AD は、Azure AD Connect 経由で組織のオンプレミス LDAP ディレクトリから ID 情報 (ユーザー、グループ) を同期します。 

* **Azure AD Connect**: オンプレミスの ID インフラストラクチャを Microsoft Azure AD に接続するためのツールです。 ウィザードとガイド付きエクスペリエンスは、接続に必要な前提条件とコンポーネントをデプロイおよび構成するのに役立ちます。 

* **カスタム コネクタ**:Generic LDAP コネクタを使用すると、Azure AD Connect 同期サービスを LDAP v3 サーバーと統合することができます。 Azure AD Connect 上にあります。

* **Active Directory**: Active Directory は、ほとんどの Windows Server オペレーティング システムに含まれているディレクトリ サービスです。 Active Directory ディレクトリ サービスを実行するサーバーは、ドメイン コントローラーと呼ばれ、Windows ドメイン内のすべてのユーザーとコンピューターを認証および認可します。

* **LDAP v3 サーバー**:ディレクトリ サービス認証に使用される会社のユーザーとパスワードを格納する LDAP プロトコルに準拠したディレクトリです。

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Azure AD との LDAP 同期を実装する

* [ハイブリッド ID ディレクトリ統合ツール](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect インストールのロードマップ](../hybrid/how-to-connect-install-roadmap.md) 

* [LDAP コネクタの概要と作成](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP コネクタをデプロイするには詳細な構成が必要であり、このコネクタは制限付きサポートの下で提供されます。 このコネクタを構成するには、Microsoft Identity Manager と特定の LDAP ディレクトリについて理解している必要があります。 
   >
   > この構成を運用環境にデプロイする必要があるお客様は、この構成に関する支援、ガイダンス、サポートが得られるよう、Microsoft コンサルティング サービスなどのパートナーと一緒に作業することをお勧めします。
