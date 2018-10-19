---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095593"
---
> [!IMPORTANT]
> - Blob およびキューの Azure AD 認証のプレビューは、非運用環境でのみ使用を対象としています。 運用環境のサービス レベル契約(SLA) は現在使用できません。 ご利用のシナリオで Azure AD 認証がまだサポートされていない場合、お使いのアプリケーションでは共有キー承認か SAS トークンを引き続き使用してください。
>
> - プレビュー期間中、RBAC ロールの割り当ての反映には最大で 5 分かかる場合があります。
>
> - Blob およびキューの操作を呼び出すときは、HTTPS を使用してAzure AD で認証する必要があります。
>
> - プレビュー リリースでは、Azure portal は、blob およびキューのデータを読み書きする Azure AD の資格情報を使用しません。 代わりに、ポータルでは、アカウント アクセス キーに引き続き依存しています。 Blob またはキュー データの表示または更新を行うには、ユーザーは[Storage Accounts - List Keys](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys)を呼び出すアクセス許可を付与する[Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)を含む RBAC ロールに割り当てられなければなりません。 Blob およびキューの共同作成者と閲覧者のロールに **listkeys**アクションがプレビュー リリースの一部として入力されていないため、Azure portal でのデータ アクセスを提供しません。 プレビュー リリース中に blob およびキュー データへの ID ベースのアクセスを調査するには、PowerShell または Azure CLI を使用します。
