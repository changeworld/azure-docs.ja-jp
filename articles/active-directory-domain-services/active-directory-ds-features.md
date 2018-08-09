---
title: 'Azure Active Directory Domain Services: 機能 | Microsoft Docs'
description: Azure Active Directory Domain Services の機能
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: a64401792fd034fde98fe1330340b1ffaa7dfcc1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505414"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>機能
Azure AD Domain Services のマネージド ドメインでは、次の機能を使用できます。

* **簡単なデプロイ操作：** 数回クリックするだけで、Azure ADディレクトリのAzure AD Domain servicesを有効にできます。 管理対象ドメインには、クラウド専用のユーザー アカウントと　オンプレミスのディレクトリから同期されているユーザー アカウントが　含まれています。
* 
  **ドメイン参加のサポート:** マネージド ドメインを使用できる Azure 仮想ネットワークにあるコンピューターを簡単にドメインに参加させることができます。 Windows クライアントおよび Windows Server オペレーティング システムで行うドメイン参加操作は、Azure AD Domain Services が提供するドメインに対してシームレスに機能します。 また、このようなドメインに対して自動化されたドメイン参加ツールを使用することもできます。
* **Azure AD ディレクトリごとに 1 つのドメイン インスタンス:** Azure AD ディレクトリごとに 1 つの Active Directory ドメインを作成できます。
* **カスタム名を使用したドメイン作成:** Azure AD Domain Services を使用して、カスタム名を指定したドメインを作成できます (例: "contoso100.com")。 確認済みまたは未確認のいずれかのドメイン名を使用できます。 必要に応じて、Azure AD ディレクトリが提供する組み込みのドメイン サフィックス (つまり、"*.onmicrosoft.com" など) を指定してドメインを作成することができます。
* **Azure AD と統合:** Azure AD ドメイン サービスへのレプリケーションを構成したり管理したりする必要がありません。 Azure AD ディレクトリのユーザー アカウント、グループ メンバーシップ、およびユーザー資格情報 (パスワード) が、Azure AD ドメイン サービスで自動的に利用可能になります。 Azure AD テナントやオンプレミスのディレクトリで新規作成されたユーザーやグループおよび変更が加えられた属性は、Azure AD ドメイン サービスに自動的に同期されます。
* **NTLM および Kerberos 認証:** NTLM および Kerberos 認証が サポート されているので、 Windows統合認証を利用するアプリケーションをデプロイできます。 
* **会社の資格情報とパスワードの使用:** Azure AD テナント内のユーザーのパスワードを Azure AD ドメイン サービスで使用できます。 ユーザーは会社の資格情報をしてコンピューターをドメインに参加させて、対話的に、またはリモート デスクトップ経由でログインし、マネージド ドメインに対して認証できます。
* **LDAP バインドと LDAP 読み取りのサポート**: Azure AD Domain Services が提供するドメイン内のユーザーを認証するために LDAP バインドを利用するアプリケーションを使用できます。 さらに、LDAP 読み取り操作を使用してディレクトリのユーザーまたはコンピューターの属性をクエリするアプリケーションも Azure AD ドメイン サービスに対して機能します。
* **セキュリティで保護された LDAP (LDAPS):** セキュリティで保護された LDAP (LDAPS) 上のディレクトリに対するアクセスを有効化できます。 セキュリティで保護された LDAP アクセスは、既定では仮想ネットワーク内で使用できます。 ただし、必要に応じて、インターネット上のセキュリティで保護された LDAP アクセスを有効化することもできます。
* **グループ ポリシー:** ユーザーおよびコンピューターのコンテナーごとに組み込みの GPO を 1 つずつ使用して、ユーザー アカウントとドメイン参加しているコンピューター向けの必須セキュリティ ポリシーに強制的に準拠させることができます。 また、独自のカスタム GPO を作成し、カスタムの組織単位に割り当てて[グループ ポリシーを管理する](active-directory-ds-admin-guide-administer-group-policy.md)こともできます。
* 
  **DNS の管理:** "AAD DC 管理者" グループのメンバーは、DNS 管理 MMC スナップインなど、使い慣れた DNS 管理ツール () を使用して、マネージド ドメインの DNS を管理できます。
* 
  **カスタム組織単位 (OU) の作成:** "AAD DC 管理者" グループのメンバーは、マネージド ドメインでカスタム OU を作成できます。 これらのユーザーにはカスタム OU に対する完全な管理者特権が付与されるため、カスタム OU のサービス アカウント、コンピューター、グループなどの追加や削除を行うことができます。
* **Azure の多くのグローバルな　リージョンで使用可能:** Azure AD Domain Services を使用できる Azure リージョンについては、 [リージョンの Azure サービス](https://azure.microsoft.com/regions/#services/) に関するページを　参照してください。
* 
  **高可用性:** Azure AD ドメイン サービスを使用して、ドメインの高可用性を実現できます。 この機能により、サービスの稼働時間とエラーに対する復元性が確実に向上します。 また、組み込みの正常性監視では、インスタンスに障害が発生したときに新しいインスタンスを作成することで障害を自動的に修正し、ドメインに継続的なサービスを提供します。
* **AD のアカウントのロックアウト　からの保護:** 2 分以内に、5 回の無効なパスワード使用が行われた場合、ユーザーアカウントは、30 分間ロック アウトされます。 アカウントは、自動的に、30 分後にロック解除されます。
* 
  **使い慣れた管理ツールの使用:** Active Directory 管理センターや Active Directory PowerShell などの使い慣れた Windows Server Active Directory 管理ツールを使用して、マネージド ドメインを管理できます。
