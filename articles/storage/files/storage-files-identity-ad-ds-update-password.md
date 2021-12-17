---
title: AD DS ストレージ アカウントのパスワードを更新する
description: ストレージ アカウントを表す Active Directory Domain Services アカウントのパスワードを更新する方法について説明します。 これにより、パスワードの有効期限が切れるときにストレージ アカウントがクリーンアップされなくなり、認証エラーを防ぐことができます。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: f54a2e7471917adb604da29e3b7fcc260ac521e0
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729484"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>AD DS のストレージ アカウント ID のパスワードを更新する

パスワードの有効期限が適用される組織単位またはドメイン内のストレージ アカウントを表す Active Directory Domain Services (AD DS) ID/アカウントを登録した場合は、パスワードの有効期間が切れる前にパスワードを変更する必要があります。 組織では、パスワードの有効期限が切れると、アカウントを削除する自動クリーンアップ スクリプトが実行される場合があります。 このため、有効期限が切れる前にパスワードを変更しなかった場合は、アカウントが削除され、Azure ファイル共有へのアクセスが失われる可能性があります。

パスワードのローテーションをトリガーするには、[AzFilesHybrid モジュール](https://github.com/Azure-Samples/azure-files-samples/releases)から `Update-AzStorageAccountADObjectPassword` コマンドを実行できます。 このコマンドは、ストレージ アカウントに対する所有者アクセス許可と、ストレージ アカウントを表す ID のパスワードを変更するための AD DS アクセス許可を持つハイブリッド ユーザーを利用して、オンプレミスの AD DS に参加している環境上で実行する必要があります。 コマンドでは、ストレージ アカウント キーのローテーションとほぼ同じアクションが実行されます。 特に、ストレージ アカウントの 2 番目の Kerberos キーが取得され、それを使用して AD DS の登録済みアカウントのパスワードが更新されます。 次に、ストレージ アカウントのターゲット Kerberos キーが再生成され、AD DS での登録済みアカウントのパスワードが更新されます。 オンプレミスの AD DS に参加している環境で、このコマンドを実行する必要があります。

パスワードのローテーションを防ぐには、ドメインでの Azure Storage アカウントのオンボード時に、その Azure Storage アカウントを AD DS の異なる組織単位に配置してください。 この組織単位でグループ ポリシーの継承を無効にして、既定のドメイン ポリシーや特定のパスワード ポリシーが適用されないようにします。

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |