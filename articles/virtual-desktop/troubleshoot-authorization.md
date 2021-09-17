---
title: Virtual Desktop の Azure Files のトラブルシューティング - Azure
description: Azure Virtual Desktop の Azure Files で発生した問題のトラブルシューティングを行う方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 31fe9f55252c00b5475bbb96cef646d0906a05f7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446616"
---
# <a name="troubleshoot-azure-files-authorization"></a>Azure Files の認証のトラブルシューティング

この記事では、Azure Active Directory (AD) を使用した Azure Files の認証に関連する一般的な問題と、それらを修正するために提案される方法について説明します。

## <a name="my-group-membership-isnt-working"></a>グループのメンバーシップが機能していません

仮想マシン (VM) を Active Directory Domain Services (AD DS) グループに追加する場合は、その VM を再起動することで、そのメンバーシップをサービス内でアクティブにする必要があります。

## <a name="i-cant-add-my-storage-account-to-my-ad-ds"></a>AD DS にストレージ アカウントを追加できません

まず、「[AD 資格情報を使用して Azure Files をマウントできない](../storage/files/storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)」を確認して、問題が一覧に表示されているかどうかを確認します。

ユーザーが問題に遭遇するおそれがある最も一般的な理由を次に示します。

- PowerShell でアカウントを作成する際に表示される警告メッセージを無視する。 警告を無視すると、新しいアカウントの設定が正しく構成されないおそれがあります。 この問題を解決するには、ストレージ アカウントに相当するドメイン アカウントを削除してから、もう一度やり直してください。

- このアカウントには、正しくない組織単位 (OU) が使用されています。 この問題を解決するには、次の構文を使用して OU 情報を再入力します。
    
    ```powershell
    DC=ouname,DC=domainprefix,DC=topleveldomain
    ```

    例:

    ```powershell
    DC=storageAccounts,DC=wvdcontoso,DC=com
    ```

- ストレージ アカウントが Azure AD に瞬時に表示されなくても、心配しないでください。 新しいストレージ アカウントが Azure AD と同期するには、通常 30 分かかりますので、しばらくお待ちください。 30 分経っても同期が行われない場合は、[次のセクション](#my-ad-ds-group-wont-sync-to-azure-ad)を参照してください。

## <a name="my-ad-ds-group-wont-sync-to-azure-ad"></a>使用している AD DS グループが Azure AD に同期しません

30 分後にご利用のストレージ アカウントが Azure AD と自動的に同期しない場合は、[こちらのスクリプト](https://github.com/stgeorgi/msixappattach/blob/master/force%20AD%20DS%20to%20Azure%20AD%20sync/force%20sync.ps1)を使用して同期を強制実行する必要があります。

## <a name="my-storage-account-says-it-needs-additional-permissions"></a>ストレージ アカウントから、追加のアクセス許可を求められています

ストレージ アカウントに追加のアクセス許可が必要である場合は、MSIX アプリのアタッチと FSLogix にアクセスするためのアクセス許可を取得していない可能性があります。 この問題を解決するには、次のいずれかのアクセス許可を自分のアカウントに確実に割り当ててください。

- **[記憶域ファイル データの SMB 共有の共同作成者]** RBAC アクセス許可。

- **[読み取りと実行]** および **[フォルダー内容の一覧表示]** NTFS アクセス許可

## <a name="next-steps"></a>次の手順

Azure Files セットアップ プロセスに関するメモリを更新する必要がある場合は、「[Azure Files のアカウントを承認する](azure-files-authorization.md)」を参照してください。