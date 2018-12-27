---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430359"
---
> [!NOTE]
> - Blob およびキューの Azure AD 認証のプレビューは、非運用環境でのみ使用を対象としています。 運用環境のサービス レベル契約(SLA) は現在使用できません。 ご利用のシナリオで Azure AD 認証がまだサポートされていない場合、お使いのアプリケーションでは共有キー承認か SAS トークンを引き続き使用してください。
>
> - プレビュー期間中、RBAC ロールの割り当ての反映には最大で 5 分かかる場合があります。
>
> - Blob およびキューの操作を呼び出すときは、HTTPS を使用してAzure AD で認証する必要があります。
>
> - Azure portal では、プレビュー リリースの一部として、Azure AD 資格情報を使用して BLOB データを読み書きすることがサポートされるようになりました。
> 
> - 現時点では、Azure portal で Azure AD 資格情報を使用してキュー データを読み書きすることはサポートされていません。 キュー データには、ストレージ アカウント キーを使用してアクセスします。
>
> - 現在 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) では、ストレージ アカウント キーを使用して BLOB データとキュー データにアクセスします。
>
> - Azure Files はドメイン参加 VM に関してのみ SMB 経由での Azure AD 認証をサポートしています (プレビュー)。 Azure Files での SMB を使用した Azure AD 認証の詳細については、「[SMB を使用した Azure Files への Azure Active Directory 認証 (プレビュー) の概要](../articles/storage/files/storage-files-active-directory-overview.md)」を参照してください。



