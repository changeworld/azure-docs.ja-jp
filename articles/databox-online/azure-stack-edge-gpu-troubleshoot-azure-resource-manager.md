---
title: Azure Stack Edge Pro GPU での Azure Resource Manager の構成のトラブルシューティング | Microsoft Docs
description: Azure Stack Edge デバイスで Azure Resource Manager の構成に関する問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: d8226d780c3caabb94f0780f3a4296765207f1b1
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987841"
---
# <a name="troubleshoot-azure-resource-manager-issues-on-an-azure-stack-edge-device"></a>Azure Stack Edge デバイスでの Azure Resource Manager の問題のトラブルシューティング 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge デバイス上のリソースの管理を妨げる可能性のある Azure Resource Manager に関する問題のトラブルシューティングを行う方法について説明します。 Azure Resource Manager では、Azure アカウントのリソースを作成、更新、削除できる管理レイヤーを提供します。
 
## <a name="azure-resource-manager-configuration-errors"></a>Azure Resource Manager の構成エラー

次のエラーは、Azure Resource Manager の構成に関する問題を示している可能性があります。 

| **問題/エラー** |  **解像度** | 
|------------|-----------------|
|一般的な問題|<li>[デバイスが正しく構成されていることを確認します](#verify-the-device-is-configured-properly)。<li> [クライアントが正しく構成されていることを確認します](#verify-the-client-is-configured-properly)。|
|Add-AzureRmEnvironment:この要求の送信中にエラーが発生しました。<br>行: 1 文字: 1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|デバイスに到達できないか、デバイスが正しく構成されていません。 デバイスとクライアントが正しく構成されていることを確認してください。 ガイダンスについては、この表の「**一般的な問題**」の行を参照してください。|
|サービスからエラーが返されました。 詳細は InnerException を確認してください:基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。 |  デバイスでの証明書の作成およびインストール時にエラーが発生しました。 詳細については、「[証明書を作成してインストールする](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)」を参照してください。 |
|操作によって無効な状態コード 'ServiceUnavailable' が返されました <br> 応答状態コードは成功を示していません:503 (サービスは利用できません)。 | このエラーは、これらのいずれかの状況の結果である可能性があります。<li>ArmStsPool が停止状態です。</li><li>Azure Resource Manager がダウンしているか、セキュリティ トークン サービスの Web サイトがダウンしています。</li><li>Azure Resource Manager クラスター リソースがダウンしています。</li><br>デバイスを再起動すると、問題が解決する場合があります。 さらにデバッグするには、[サポート パッケージを収集](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)します。|
|AADSTS50126:ユーザー名またはパスワードが無効です。<br>トレース ID:29317da9-52fc-4ba0-9778-446ae5625e5a<br>関連付け ID:1b9752c4-8cbf-4304-a714-8a16527410f4<br>タイムスタンプ:2019-11-15 09:21:57Z:リモート サーバーがエラー(400) 要求が正しくありません。<br>行: 1 文字: 1 |このエラーは、これらのいずれかの状況の結果である可能性があります。<li>ユーザー名とパスワードが無効の場合は、[Azure portal から Azure Storage Manager のパスワードをリセット](./azure-stack-edge-gpu-set-azure-resource-manager-password.md)してから、正しいパスワードを使用します。<li>テナント ID が無効の場合は、テナント ID が`c0257de7-538f-415c-993a-1b87a031879d` に設定されていることを確認します。</li>|
|connect-AzureRmAccount:AADSTS90056:リソースが無効になっているか、存在しません。 アプリのコードをチェックして、アクセスしようとしているリソースの正確なリソース URL を指定していることを確認します。<br>トレース ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>関連付け ID:75c8ef5a-830e-48b5-b039-595a96488ff9 タイムスタンプ:2019-11-18 07:00:51Z:リモート サーバーがエラー(400) Bad を返しました |`Add-AzureRmEnvironment` コマンドで使用されている Azure Resource Manager エンドポイントが正しくありません。<br>Azure Resource Manager エンドポイントを確認するには、デバイスのローカル Web UI の **[デバイス]** ページで **[デバイス エンドポイント]** を確認します。<br>PowerShell の手順については、「[Azure Resource Manager 環境を設定する](azure-stack-edge-gpu-connect-resource-manager.md#step-7-set-azure-resource-manager-environment)」を参照してください。 |
|クラウドからエンドポイントを取得できません。<br>ネットワークに接続していることを確認してください。 エラーの詳細:HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005):次の URL で最大再試行回数を超えました: /metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake:Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |このエラーは、主に Mac または Linux 環境で発生します。 このエラーは、PEM 形式の証明書が Python 証明書ストアに追加されていないことが原因で発生します。 |


## <a name="troubleshoot-general-issues-with-azure-resource-manager"></a>Azure Resource Manager に関する一般的な問題のトラブルシューティング

Azure Resource Manager に関する一般的な問題については、デバイスとクライアントが正しく構成されていることを確認します。 エンド ツー エンドの手順については、[Azure Stack Edge Pro GPU デバイスの Azure Resource Manager への接続](azure-stack-edge-gpu-connect-resource-manager.md)に関する記事を参照してください。


### <a name="verify-the-device-is-configured-properly"></a>デバイスが正しく構成されていることの確認

1. ローカル UI から、デバイスのネットワークが正しく構成されていることを確認します。

2. [すべてのエンドポイントで証明書が更新されていることを確認します](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)。

3. ローカル UI の **[デバイス]** ページから、Azure Resource Manager の管理およびログイン エンドポイントを取得します。

4. デバイスがアクティブ化され、Azure に登録されていることを確認します。


### <a name="verify-the-client-is-configured-properly"></a>クライアントが正しく構成されていることを確認します

1. [正しい PowerShell バージョンがインストールされていることを確認します](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)。

2. [正しい PowerShell モジュールがインストールされていることを確認します](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)。

3. Azure Resource Manager とログイン エンドポイントに到達できることを確認します。 エンドポイントに対して ping を試すことができます。 次に例を示します。

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   到達できない場合は、[DNS およびホスト ファイルのエントリを追加](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)します。
   
4. [クライアント証明書がインストールされていることを確認します](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)。

5. PowerShell を使用している場合は、この PowerShell コマンドを実行してデバッグ設定を有効にし、詳細メッセージを確認します。 

    `$debugpreference = "continue"`


## <a name="next-steps"></a>次のステップ

- [デバイスのアクティブ化に関する問題をトラブルシューティングする](azure-stack-edge-gpu-troubleshoot-activation.md)。
- [デバイスの問題をトラブルシューティングする](azure-stack-edge-gpu-troubleshoot.md)。