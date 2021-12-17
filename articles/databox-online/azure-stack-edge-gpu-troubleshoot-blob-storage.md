---
title: Azure Stack Edge Pro GPU の BLOB ストレージのトラブルシューティング | Microsoft Docs
description: Azure Stack Edge デバイスの BLOB ストレージに関する問題のトラブルシューティング方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 2a77d4992d5ee2e505f9e3f29112d45c3776d8d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438764"
---
# <a name="troubleshoot-blob-storage-issues-for-an-azure-stack-edge-device"></a>Azure Stack Edge デバイスの BLOB ストレージに関する問題のトラブルシューティング 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、お使いの Azure Stack Edge デバイスの BLOB ストレージに関する問題のトラブルシューティング方法について説明します。 

## <a name="errors-for-blob-storage-on-device"></a>デバイス上の BLOB ストレージのエラー 

ここでは、Azure Stack Edge デバイス上の BLOB ストレージに関連するエラーについて説明します。

| **問題/エラー** |  **解像度** | 
|--------------------|-----------------|
|Unable to retrieve child resources. (子リソースを取得できません。) The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。)| **[編集]** メニューから **[Target Azure Stack APIs]\(Azure Stack API を対象とする\)** を選択します。 その後、Azure Storage Explorer を再起動します。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が、Windows ではパス `C:\Windows\System32\drivers\etc\hosts`、Linux では `/etc/hosts` にある hosts ファイルに追加されていることを確認します。|
|Unable to retrieve child resources. (子リソースを取得できません。)<br> Details: self-signed certificate (詳細: 自己署名証明書) |次の手順に従って、ご使用のデバイス用の SSL 証明書を Azure Storage Explorer にインポートします。 <ol><li>[証明書を生成してダウンロードします](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。</li><li>**[編集]** メニューから、 [SSL 証明書] 、 **[証明書のインポート]** の順に選択します。</li></ol>|
|AzCopy コマンドが 1 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `C:\Windows\System32\drivers\etc\hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 1 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |次の手順に従って、ご使用のデバイス用の SSL 証明書を Azure Storage Explorer にインポートします。 <ol><li>[証明書を生成してダウンロードします](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。</li><li>**[編集]** メニューから、 [SSL 証明書] 、 **[証明書のインポート]** の順に選択します。</li></ol>|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `/etc/hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location… The SSL connection could not be established`. |次の手順に従って、ご使用のデバイス用の SSL 証明書を Azure Storage Explorer にインポートします。 <ol><li>[証明書を生成してダウンロードします](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)。</li><li>**[編集]** メニューから、 [SSL 証明書] 、 **[証明書のインポート]** の順に選択します。</li></ol>|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `/etc/hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のエラーが表示されます。`Error parsing source location… The SSL connection could not be established`|ご使用のデバイス用の SSL 証明書をシステムの証明書ストアにインポートします。 詳細については、「[証明書のダウンロード](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)」を参照してください。|
|The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。)|インストールしたバージョンの Python 用 Microsoft Azure Storage ライブラリが Azure Stack Edge でサポートされていません。 サポートされているライブラリ バージョンについては、「 [サポートされている Azure クライアント ライブラリ](azure-stack-edge-gpu-system-requirements-rest.md#supported-azure-client-libraries)」を参照してください。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …| Python を実行する前に、REQUESTS_CA_BUNDLE 環境変数を Base64 でエンコードされた SSL 証明書ファイルのパスに設定してください ([証明書のダウンロード](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)方法を参照してください)。 たとえば、以下を実行します。<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>あるいは、証明書をシステムの証明書ストアに追加し、この環境変数をそのストアのパスに設定します。 たとえば、Ubuntu 上では以下を実行します。<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`|
|The connection times out. (接続がタイムアウトになりました。)|ご自身のデバイスにサインインし、ロックが解除されているかどうかを確認します。 デバイスは再起動されるたびに、ユーザーがサインインするまでロックされた状態になります。|
|ストレージ アカウントを作成または更新できませんでした。 ストレージ アカウントのアクセス キーが有効であることを確認してください。 必要に応じて、デバイスのキーを更新します。|ストレージ アカウント キーを同期します。 [ここ](azure-stack-edge-gpu-manage-storage-accounts.md#sync-storage-keys)に記載されている手順に従います。|

## <a name="next-steps"></a>次のステップ

- [デバイスのアップロードと更新に関するエラーのトラブルシューティング](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-device-upload-and-refresh-errors)。
