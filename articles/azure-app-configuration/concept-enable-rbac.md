---
title: Azure Active Directory を使用して Azure App Configuration へのアクセスを承認する
description: RBAC を有効にして、Azure App Configuration インスタンスへのアクセスを承認します
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473199"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Azure Active Directory を使用して Azure App Configuration へのアクセスを承認する
Azure App Configuration では、App Configuration インスタンスに対する要求を承認するための Azure Active Directory (Azure AD) の使用がサポートされています。  Azure AD を使用することにより、ロールベースのアクセス制御 (RBAC) を使用してセキュリティ プリンシパルにアクセス許可を付与することができます。  セキュリティ プリンシパルは、ユーザー プリンシパル、または[アプリケーション サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)のいずれかになります。  ロールおよびロールの割り当ての詳細については、[各種ロールについて](../role-based-access-control/overview.md)のページを参照してください。

## <a name="overview"></a>概要
App Configuration にアクセスするためにセキュリティ プリンシパル (ユーザーまたはアプリケーション) によって行われる要求は、承認される必要があります。  Azure AD では、リソースへのアクセスは 2 段階のプロセスです。
1. セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。  トークンを要求するリソース名は、`https://login.microsoftonline.com/{tenantID}` です。この場合の `{tenantID}` は、サービス プリンシパルが属する Azure Active Directory テナント ID と一致します。
2. 指定されたリソースへのアクセスを承認するために、トークンが要求の一部として App Configuration サービスに渡されます。

認証の手順により、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれる必要があります。  アプリケーションが Azure エンティティ (Azure Functions アプリ、Azure Web アプリ、Azure VM など) 内で実行されている場合、マネージド ID を使用してリソースにアクセスできます。  マネージド ID によって Azure App Configuration に対して行われる要求を認証する方法については、[Azure Active Directory と Azure リソースのマネージド ID を使用して Azure App Configuration リソースへのアクセスを承認する](howto-integrate-azure-managed-service-identity.md)方法に関するページをご覧ください。

承認の手順では、セキュリティ プリンシパルに 1 つ以上の RBAC ロールを割り当てる必要があります。 Azure App Configuration は、App Configuration リソースに対するアクセス許可セットを含む RBAC ロールを提供します。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 RBAC ロールの詳細については、「[Azure App Configuration の組み込み RBAC ロール](#built-in-rbac-roles-for-azure-app-configuration)」を参照してください。 

## <a name="assign-rbac-roles-for-access-rights"></a>アクセス権に RBAC ロールを割り当てる
Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスの範囲は、App Configuration リソースに制限されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、またはアプリケーション サービス プリンシパル、または[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure App Configuration の組み込み RBAC ロール
Azure には、Azure AD と OAuth を使用した、App Configuration データへのアクセスを承認するために、次の組み込み RBAC ロールが用意されています。

- Azure App Configuration データ所有者: このロールを使用して、App Configuration リソースへの読み取り/書き込みアクセスを付与します。
- App Configuration データ閲覧者: このロールを使用して、App Configuration リソースへの読み取りアクセスを付与します。
- 共同作成者: このロールを使用して、サービスへの管理者アクセスを付与します。App Configuration インスタンスに格納されているデータへのアクセスは付与されません。

## <a name="next-steps"></a>次のステップ
App Configuration サービスを管理するための [マネージド ID](howto-integrate-azure-managed-service-identity.md) の使用について詳しく説明します。