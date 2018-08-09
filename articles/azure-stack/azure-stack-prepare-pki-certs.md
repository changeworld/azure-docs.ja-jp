---
title: Azure Stack 統合システム デプロイのための Azure Stack 公開キー インフラストラクチャ証明書を準備する | Microsoft Docs
description: Azure Stack 統合システムの Azure Stack PKI 証明書を準備する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 387c4ce51d7bc9b618bb87fd347b9448926452b7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426797"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>デプロイ用の Azure Stack PKI 証明書の準備
[任意の CA から取得した](azure-stack-get-pki-certs.md)証明書ファイルは、Azure Stack の証明書要件に一致するプロパティを使用してインポートおよびエクスポートする必要があります。


## <a name="prepare-certificates-for-deployment"></a>デプロイ用の証明書を準備する
次の手順を使って、Azure Stack PKI 証明書を準備し、検証します。 

### <a name="import-the-certificate"></a>証明書のインポート

1.  [任意の CA から取得した](azure-stack-get-pki-certs.md)オリジナル バージョンの証明書を、デプロイ ホスト上のディレクトリにコピーします。 
  > [!WARNING]
  > CA によって直接提供されたファイルから何らかの方法で既にインポート、エクスポート、または変更されているファイルをコピーすることはできません。

1.  証明書を右クリックして、CA からどのように証明書が配信されたかによって、**[証明書のインストール]** または **[PFX のインストール]** を選択します。

1. **証明書のインポート ウィザード**で、インポートの場所として **[ローカル コンピューター]** を選択します。 **[次へ]** を選択します。 次の画面で、再度次へをクリックします。

    ![ローカル コンピューターのインポート場所](.\media\prepare-pki-certs\1.png)

1.  **[Place all certificate in the following store]\(すべての証明書を次のストアに配置する\)** を選択し、場所として **[エンタープライズの信頼]** を選択します。 **[OK]** をクリックして証明書ストアの選択ダイアログ ボックスを閉じ、**[次へ]** をクリックします。

    ![証明書ストアの構成](.\media\prepare-pki-certs\3.png)

    a. PFX をインポートする場合は、ダイアログがさらに表示されます。 **[秘密キーの保護]** ページで、証明書ファイルのパスワードを入力し、**[Mark this key as exportable. This allows you to back up or transport your keys at a later time]\(このキーをエクスポート可能としてマークします。これにより、後でキーをバックアップまたは転送できるようになります\)** オプションを有効にします。 **[次へ]** を選択します。

    ![キーをエクスポート可能としてマークする](.\media\prepare-pki-certs\2.png)

1. [完了] をクリックしてインポートを完了します。

### <a name="export-the-certificate"></a>証明書をエクスポートする

証明書管理者 MMC コンソールを開き、ローカル コンピューターの証明書ストアに接続します。

1. Microsoft 管理コンソールを開き、Windows 10 の [スタート] メニューを右クリックし、[実行] をクリックします。 「**mmc**」と入力し、[OK] をクリックします。

1. [ファイル]、[スナップインの追加と削除] をクリックし、証明書を選択して [追加] をクリックします。

    ![証明書スナップインの追加](.\media\prepare-pki-certs\mmc-2.png)
 
1. コンピューター アカウントを選択し、[次へ] をクリックし、ローカル コンピューターを選択して完了します。 [スナップインの追加と削除] ページをクリックして閉じます。

    ![証明書スナップインの追加](.\media\prepare-pki-certs\mmc-3.png)

1. [証明書]、[エンタープライズの信頼] の順に進み、[証明書の場所] を参照します。 右側に証明書が表示されることを確認します。

1. 証明書管理者コンソールのタスク バーから、**[アクション]** > **[All Tasks]\(すべてのタスク\)** > **[エクスポート]** を選択します。 **[次へ]** を選択します。

  > [!NOTE]
  > 所持している Azure Stack 証明書により、この手順は何度か完了する必要があります。

1. **[はい、秘密キーをエクスポートします]** を選択し、**[次へ]** をクリックします。

1. [エクスポート ファイルの形式] セクションで、**[すべての拡張プロパティをエクスポートする]** を選択し、**[次へ]** をクリックします。

1. **[パスワード]** を選択し、証明書のパスワードを指定します。 このパスワードはデプロイ パラメーターとして使用されるので、忘れないようにしてください。 **[次へ]** を選択します。

1. エクスポートする pfx ファイルのファイル名と場所を選択します。 **[次へ]** を選択します。

1. **[完了]** を選択します。

## <a name="next-steps"></a>次の手順
[PKI 証明書の検証](azure-stack-validate-pki-certs.md)