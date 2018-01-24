---
title: "管理ポータルで Azure Stack のバックアップを有効にする | Microsoft Docs"
description: "エラーが発生した場合に Azure Stack を復元できるように、管理ポータルでインフラストラクチャ バックアップ サービスを有効にします。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: a5a9757d871c343ba663862de7b6d75b9dd21c31
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>管理ポータルで Azure Stack のバックアップを有効にする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack でバックアップを生成できるように、管理ポータルでインフラストラクチャ バックアップ サービスを有効にします。 障害が発生した場合に、これらのバックアップを使用して環境を復元できます。

## <a name="enable-backup"></a>バックアップの有効化

1. Azure Stack 管理ポータル ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)) を開きます。
2. **[その他のサービス]** > **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** の順に選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。

    ![Azure Stack - バックアップ コントローラーの設定](media\azure-stack-backup\azure-stack-backup-settings.png)が必要です。

3. **[Backup storage location]\(バックアップの保存場所\)** にバックアップ先のパスを入力します。 別のデバイスでホストされるファイル共有のパスの場合、汎用名前付け規則 (UNC) の文字列を使用する必要があります。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。 サービスの場合は、IP アドレスを使用できます。 障害発生時にバックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。
    > [!Note]  
    > 使用する環境で Azure Stack インフラストラクチャ ネットワークからエンタープライズ環境への名前解決がサポートされている場合は、IP ではなく FQDN を使用できます。
4. **[ユーザー名]** にドメインとユーザー名を入力します。 たとえば、「`Contoso\administrator`」のように入力します。
5. ユーザーの**パスワード**を入力します。
5. **[パスワードの確認]** にもう一度パスワードを入力します。
6. **[暗号化キー]** ボックスに事前共有キーを入力します。 バックアップ ファイルはこのキーを使用して暗号化されます。 このキーは安全な場所に保存してください。 初めて事前共有キーを設定する場合、または今後キーを交換する場合には、このインターフェイスでキーを表示することはできません。 事前共有キーの生成手順の詳細については、「[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key)」でスクリプトをご覧ください。 
7. **[OK]** を選択して、バックアップ コントローラーの設定を保存します。

バックアップを実行するには、Azure Stack ツールをダウンロードし、Azure Stack 管理ノードで PowerShell コマンドレット **Start-AzSBackup** を実行する必要があります。 詳細については、「[Back up Azure Stack (Azure Stack のバックアップ)](azure-stack-backup-back-up-azure-stack.md )」をご覧ください。

## <a name="next-steps"></a>次の手順

 - 「[Back up Azure Stack (Azure Stack のバックアップ)](azure-stack-backup-back-up-azure-stack.md )」でバックアップの実行方法を学びます。
- 「[Confirm backup completed in administration portal (管理ポータルでのバックアップ完了の確認)](azure-stack-backup-back-up-azure-stack.md )」でバックアップが実行されたことを確認する方法について学びます。
