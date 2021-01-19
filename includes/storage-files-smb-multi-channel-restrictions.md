---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052875"
---
Azure ファイル共有の SMB マルチチャネルには、現在、次の制限があります。
- ローカルでの冗長 FileStorage アカウントでのみ使用できます。
- Windows クライアントでのみサポートされます。 
- 最大チャネル数は 4 です。
- SMB ダイレクトはサポートされていません。
- ストレージ アカウントのプライベート エンドポイントはサポートされていません。
- オンプレミスの Active Directory Domain Services (AD DS) または Azure AD DS の [ID ベースの認証](../articles/storage/files/storage-files-active-directory-overview.md)が Azure Files に対して有効になっているストレージ アカウントの場合は、SMB クライアントが Windows エクスプローラーを使用して、ディレクトリとファイルに対する NTFS アクセス許可を構成することができません。
    - アクセス許可を構成する代わりに、Windows [icacls](/windows-server/administration/windows-commands/icacls) ツールまたは [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) コマンドを使用してください。

