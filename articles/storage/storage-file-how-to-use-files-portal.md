---
title: "Azure Portal から Azure File Storage を管理する方法 | Microsoft Docs"
description: "Azure Portal を使用して Azure File Storage を管理する方法を説明します。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d8ffb4359b0efe8da2f3bccb81c987bdeedf1a39
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="how-to-use-azure-file-storage-from-the-azure-portal"></a>Azure Portal から Azure File Storage を使用する方法
[Azure Portal](https://portal.azure.com) には、Azure File Storage を管理するためのユーザー インターフェイスが用意されています。 Web ブラウザーから、次の操作を実行できます。

* ファイル共有の作成
* ファイル共有からのファイルのアップロードおよびダウンロード
* 各ファイル共有の実際の使用状況の監視
* ファイル共有のサイズ クォータの調整
* Windows または Linux クライアントからファイル共有をマウントするために使用するマウンド コマンドをコピーします。

## <a name="create-file-share"></a>ファイル共有の作成
1. Azure ポータルにサインインします。
2. ナビゲーション メニューで、**[ストレージ アカウント]** または **[ストレージ アカウント (クラシック)]** をクリックします。
    
    ![Screenshot that shows how to create file share in the portal](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. ストレージ アカウントを選択します。

    ![Screenshot that shows how to create file share in the portal](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. [Files] サービスを選択します。

    ![Screenshot that shows how to create file share in the portal](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. [ファイル共有] をクリックし、リンクをクリックして最初のファイル共有を作成します。

    ![Screenshot that shows how to create file share in the portal](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. 最初のファイル共有を作成するには、ファイル共有名とファイル共有のサイズ (最大 5120 GB) を入力します。 作成されたファイル共有は、SMB 2.1 または SMB 3.0 をサポートしている任意のファイル システムからマウントできます。 ファイル共有の **[クォータ]** をクリックすると、ファイルのサイズを最大 5,120 GB まで変更することができます。 Azure File Storage を使用するストレージ コストを見積もるには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してください。

    ![Screenshot that shows how to create file share in the portal](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>ファイルのアップロードとダウンロード
1. 既に作成した 1 つのファイル共有を選択します。

    ![Screenshot that shows how to upload and download files from the portal](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. **[アップロード]** をクリックして、ファイルをアップロードするためのユーザー インターフェイスを開きます。

    ![Screenshot that shows how to upload files from the portal](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>ファイル共有への接続
-  **[接続]** をクリックし、Windows および Linux からファイル共有にマウントするためのコマンド ラインを取得します。 Linux ユーザーの場合、他の Linux ディストリビューションのマウント操作手順の詳細については、[Linux で Azure File Storage を使用する方法](storage-how-to-use-files-linux.md)に関するページも参照することができます。

    ![Screenshot that shows how to mount the file share](media/storage-file-how-to-use-files-portal/use-files-portal-connect.png)
-  Windows または Linux 上のファイル共有にマウントするためのコマンドをコピーして、Azure VM またはオンプレミス マシンから実行できます。

    ![Windows と Linux のマウント コマンドを示すスクリーンショット](media/storage-file-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**ヒント:**  
マウントのためのストレージ アカウント アクセス キーを確認するには、接続ページの下部の **[このストレージ アカウントのアクセス キーを表示する]** をクリックします。

## <a name="see-also"></a>関連項目
Azure File Storage の詳細については、次のリンクを参照してください。

* [FAQ](storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-file-connection-problems.md)
