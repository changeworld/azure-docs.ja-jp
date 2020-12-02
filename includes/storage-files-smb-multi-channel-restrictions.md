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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995486"
---
Azure ファイル共有の SMB マルチチャネルには、現在、次の制限があります。
- ローカルでの冗長 FileStorage アカウントでのみ使用できます。
- Windows クライアントでのみサポートされます。 
- 最大チャネル数は 4 です。
- SMB ダイレクトはサポートされていません。
- オンプレミスの Active Directory Domain Services (AD DS) または Azure AD DS の [ID ベースの認証](../articles/storage/files/storage-files-active-directory-overview.md)が Azure Files に対して有効になっているストレージ アカウントの場合は、SMB クライアントが Windows エクスプローラーを使用して、ディレクトリとファイルに対する NTFS アクセス許可を構成することができません。
    - アクセス許可を構成する代わりに、Windows [icacls](/windows-server/administration/windows-commands/icacls) ツールまたは [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) コマンドを使用してください。

