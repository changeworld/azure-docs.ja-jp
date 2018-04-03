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
ms.openlocfilehash: dadb443f8b7739e3a18c0d3beb558d8c42e9d19c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>デプロイ用の Azure Stack PKI 証明書の準備
[任意の CA から取得した](azure-stack-get-pki-certs.md)証明書ファイルは、Azure Stack の証明書要件に一致するプロパティを使用してインポートおよびエクスポートする必要があります。


## <a name="prepare-certificates-for-deployment"></a>デプロイ用の証明書を準備する
次の手順を使用して、Azure Stack PKI 証明書を準備し、検証します。 

1.  [任意の CA から取得した](azure-stack-get-pki-certs.md)オリジナル バージョンの証明書を、デプロイ ホスト上のディレクトリにコピーします。 
  > [!WARNING]
  > CA によって直接提供されたファイルから何らかの方法で既にインポート、エクスポート、または変更されているファイルをコピーすることはできません。

2.  証明書をローカル コンピューターの証明書ストアにインポートします。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。  証明書を右クリックし、 **[PFX のインストール]**を選択します。

    b.  **証明書のインポート ウィザード**で、インポートの場所として **[ローカル コンピューター]** を選択します。 **[次へ]**を選択します。

    ![ローカル コンピューターのインポート場所](.\media\prepare-pki-certs\1.png)

    c.  **[インポートするファイルの選択]** ページで **[次へ]** を選択します。

    d.  **[秘密キーの保護]** ページで、証明書ファイルのパスワードを入力し、**[Mark this key as exportable. This allows you to back up or transport your keys at a later time]\(このキーをエクスポート可能としてマークします。これにより、後でキーをバックアップまたは転送できるようになります\)** オプションを有効にします。 **[次へ]**を選択します。

    ![キーをエクスポート可能としてマークする](.\media\prepare-pki-certs\2.png)

    e.  **[Place all certificate in the following store]\(すべての証明書を次のストアに配置する\)** を選択し、場所として **[エンタープライズの信頼]** を選択します。 **[OK]** をクリックして証明書ストアの選択ダイアログ ボックスを閉じ、**[次へ]** をクリックします。

    ![証明書ストアの構成](.\media\prepare-pki-certs\3.png)

  f.    **[完了]** をクリックして証明書のインポート ウィザードを完了します。

  g.    デプロイ用に提供するすべての証明書について、このプロセスを繰り返します。

3. Azure Stack の要件を満たした PFX ファイル形式に証明書をエクスポートします。

  a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。    証明書管理者 MMC コンソールを開き、ローカル コンピューターの証明書ストアに接続します。

  b.    **[エンタープライズの信頼]** ディレクトリに移動します。

  c.    上記の手順 2 でインポートした証明書のいずれかを選択します。

  d.    証明書管理者コンソールのタスク バーから、**[アクション]** > **[All Tasks]\(すべてのタスク\)** > **[エクスポート]** を選択します。

  e.    **[次へ]**を選択します。

  f.    **[はい、秘密キーをエクスポートします]** を選択し、**[次へ]** をクリックします。

  g.    [エクスポート ファイルの形式] セクションで、**[すべての拡張プロパティをエクスポートする]** を選択し、**[次へ]** をクリックします。

  h.    **[パスワード]** を選択し、証明書のパスワードを指定します。 このパスワードはデプロイ パラメーターとして使用されるので、忘れないようにしてください。 **[次へ]**を選択します。

  i.    エクスポートする pfx ファイルのファイル名と場所を選択します。 **[次へ]**を選択します。

  j.    **[完了]** を選択します。

  k.    上記の手順 2 でデプロイ用にインポートしたすべての証明書について、この手順を繰り返します。

## <a name="next-steps"></a>次の手順
[PKI 証明書の検証](validate-pki-certs.md)