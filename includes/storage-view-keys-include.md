---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521279"
---
Azure portal からストレージ アカウントのアクセス キーまたは接続文字列を表示およびコピーするには:

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 自分のストレージ アカウントを探します。
3. **[設定]** で **[アクセス キー]** を選択します。 アカウント アクセス キーと、各キーの完全な接続文字列が表示されます。
4. **[key1]** で **[キー]** 値を見つけ、 **[コピー]** ボタンをクリックしてアカウント キーをコピーします。
5. あるいは、接続文字列全体をコピーできます。 **[Key1]** の **[接続文字列]** の値を見つけて **[コピー]** ボタンをクリックし、接続文字列をコピーします。

    ![Azure portal でアクセス キーを表示する方法を示したスクリーンショット](media/storage-view-keys-include/portal-connection-string.png)

どちらのキーを使用して Azure Storage にアクセスすることもできますが、一般的には、最初のキーを使用し、キーのローテーション時に 2 番目のキーの使用を予約することをお勧めします。

アカウントのアクセス キーを表示または読み込むには、ユーザーがサービス管理者であるか、**Microsoft. Storage/storageAccounts/listkeys/action** を含む RBAC ロールが割り当てられている必要があります。 このアクションが含まれる組み込み RBAC ロールには、**所有者**、**共同作成者**、および**ストレージ アカウント キー オペレーターのサービス ロール**があります。 サービス管理者ロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD ロール](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。 Azure Storage 用の組み込み RBAC ロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../articles/role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。
