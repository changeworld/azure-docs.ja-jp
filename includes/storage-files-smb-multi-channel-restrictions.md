---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/26/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 04ded4340eef0bfe5bc91ee2e3f2552975df2e05
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065651"
---
Azure ファイル共有の SMB マルチチャネルには、現在、次の制限があります。
- Windows クライアントでのみサポートされます。 
- 最大チャネル数は 4 です。
- SMB ダイレクトはサポートされていません。
- ストレージ アカウントのプライベート エンドポイントはサポートされていません。
- オンプレミスの Active Directory Domain Services (AD DS) または Azure AD DS の [ID ベースの認証](../articles/storage/files/storage-files-active-directory-overview.md)が Azure Files に対して有効になっているストレージ アカウントの場合は、SMB クライアントが Windows エクスプローラーを使用して、ディレクトリとファイルに対する NTFS アクセス許可を構成することができません。
    - アクセス許可を構成する代わりに、Windows [icacls](/windows-server/administration/windows-commands/icacls) ツールまたは [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) コマンドを使用してください。

