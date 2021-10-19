---
title: ロールとリソースのアクセス制御
titleSuffix: Azure AD B2C
description: ロールを使用してリソースへのアクセスを制御する方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: b9a4e9cf2fa8c8cd91fed738ec14e88d18063044
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620911"
---
# <a name="roles-and-resource-access-control"></a>ロールとリソースのアクセス制御

アクセス制御戦略を計画するときは、リソースにアクセスするために必要な最小限の特権をユーザーに割り当てることをお勧めします。 次の表は、Azure AD B2C テナントのプライマリ リソースと、それらを管理するユーザーに最も適した管理者ロールを説明しています。

|リソース  |説明  |Role  |
|---------|---------|---------|
|[アプリケーションの登録](tutorial-register-applications.md) | Azure AD B2C 内で、Web、モバイル、およびネイティブのアプリケーション登録のすべての側面を作成し、管理します。|[アプリケーション管理者](../active-directory/roles/permissions-reference.md#application-administrator)|
|[ID プロバイダー](add-identity-provider.md)| [ローカル ID プロバイダー](identity-provider-local.md)と外部のソーシャルまたはエンタープライズ ID プロバイダーを構成します。 | [外部 ID プロバイダー管理者](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API コネクタ](add-api-connector.md)| ユーザー フローを Web API と統合してユーザー エクスペリエンスをカスタマイズし、外部システムと統合します。|[外部 ID ユーザー フロー管理者](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[会社のブランド](customize-ui.md#configure-company-branding)| ユーザー フローの各ページをカスタマイズします。| [全体管理者](../active-directory/roles/permissions-reference.md#global-administrator)|
|[ユーザー属性](user-flow-custom-attributes.md)| すべてのユーザー フローに使用可能なカスタム属性を追加するか、削除します。| [外部 ID ユーザー フロー属性管理者](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|ユーザーの管理| この記事で説明されているように、[コンシューマー アカウント](manage-users-portal.md) と管理アカウントを管理します。| [ユーザー管理者](../active-directory/roles/permissions-reference.md#user-administrator)|
|ロールと管理者| Azure AD B2C ディレクトリでロールの割り当てを管理します。 Azure AD B2C ロールに割り当てることができるグループを作成し、管理します。 |[ブローバル管理者](../active-directory/roles/permissions-reference.md#global-administrator)、[特権ロール管理者](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[ユーザー フロー](user-flow-overview.md)|サインアップ、サインイン、プロファイル編集などの一般的な ID タスクを短時間で構成し、有効にします。| [外部 ID ユーザー フロー管理者](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[カスタム ポリシー](user-flow-overview.md)| Azure AD B2C ですべてのカスタムポリシーの作成、読み取り、更新、および削除を行います。| [B2C IEF ポリシー管理者](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[ポリシー キー](policy-keys-overview.md)|カスタム ポリシーで使用されるトークン、クライアント シークレット、証明書、パスワードの署名と検証のための暗号化キーを追加し、管理します。|[B2C IEF キーセット管理者](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|