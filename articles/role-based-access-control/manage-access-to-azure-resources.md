---
title: Azure Active Directory による Azure リソースへのアクセス管理
description: Azure Active Directory のさまざまな機能を使用して Azure リソースへのアクセスを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: f8f8af1380dc47a4a97845d9d47ac17bd4bba3e0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Azure Active Directory による Azure リソースへのアクセス管理

クラウド リソースのへのアクセスの ID と管理は、クラウドが使用している組織にとって重要な機能です。 Azure Active Directory (Azure AD) は Microsoft Azure の ID およびアクセス システムです。  

Azure AD がサポートしている機能領域を調べる前に、次のビデオ「SSO、ロール ベースのアクセス制御、および条件を使用した Azure クラウドへのアクセスのロック」をご覧ください。 その内容は次のとおりです。

- オンプレミスの Active Directory を使用した Azure ポータルへのシングル サインオンを構成するためのベストプラクティス。
- サブスクリプション内のリソースに対する粒度の細かいアクセス制御を行うための Azure RBAC の使用。
- Azure AD の条件付きアクセスを使用した強力な認証ルールの適用。
- 管理対象サービス ID の概念。この概念によって、Azure リソースは Azure サービスで自動的に認証され、開発者は API キーまたはシークレットを処理する必要はありません。

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>機能領域
Azure AD では、Azure リソースへのアクセスを管理するため、次の機能を示します。

|||
|---|---|
| [Azure AD テナントとサブスクリプション間のリレーションシップ](rbac-and-directory-admin-roles.md) | Azure AD が、Azure サブスクリプションのユーザーおよびグループの信頼されたソースになる方法について説明します。 |
| [ロール ベースのアクセス制御 (RBAC)](overview.md) | ユーザー、グループ、またはサービス プリンシパルに割り当てられた役割による細かいアクセス管理を提供します。 |
| [RBAC と Privileged Identity Management](pim-azure-resource.md) | 特権ロールを適時に割り当てることによって、特権アクセスを制御します。 |
| [Azure 管理の条件付きアクセス](conditional-access-azure-management.md) | Azure 管理エンドポイントへのアクセスを許可またはブロックする条件付きアクセス ポリシーを設定します。 |
| [管理サービス ID (MSI)](../active-directory/pp/msi-overview.md) | コードに資格情報を格納する必要がないように、仮想マシンなどの Azure リソースに独自の ID を提供します。 |

 