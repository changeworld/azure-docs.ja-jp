---
title: インクルード ファイル
description: 前提条件を満たしたインクルード ファイル
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426915"
---
> [!IMPORTANT]
> **この記事のタスクを完了する前に、Azure AD Domain Services に対するパスワード ハッシュ同期を有効にしてください。**
>
> Azure AD ディレクトリにおけるユーザーの種類に応じて、以下の手順に従います。 ご利用の Azure AD ディレクトリに、クラウド専用ユーザー アカウントと同期済みユーザー アカウントが混在している場合は、両方の手順を実行してください。 B2B ゲスト アカウント (たとえば、許可する別の ID プロバイダーからの Gmail や MSA) の使用を試みる場合は、以下の操作を実行できないことがあります。これらはディレクトリ内のゲスト アカウントであるため、マネージド ドメインに同期されたこれらのユーザーのパスワードがないからです。 パスワードを含むこれらのアカウントに関する完全な情報は、Azure AD の外部のものとなります。この情報が Azure AD 内にないため、マネージド ドメインへの同期も行われません。 
> - [クラウド専用ユーザー アカウントの場合](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [オンプレミス ディレクトリとの間で同期されるユーザー アカウントの場合](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
