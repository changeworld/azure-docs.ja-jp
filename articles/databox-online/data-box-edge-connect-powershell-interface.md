---
title: Microsoft Azure Data Box Edge デバイスに Windows PowerShell インターフェイス経由で接続し、管理する | Microsoft Docs
description: Data Box Edge に Windows PowerShell インターフェイス経由で接続し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b3effdbace2be582bfe85d0402088f8aa0d96fe7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556454"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell-preview"></a>Azure Data Box Edge デバイスを Windows PowerShell 経由で管理する (プレビュー)

Azure Data Box Edge ソリューションにより、データを処理してネットワーク経由で Azure に送信できます。 この記事では、Data Box Edge デバイスの構成と管理のタスクをいくつか説明します。 Azure portal、ローカル Web UI、または Windows PowerShell インターフェイスを使用してデバイスを管理できます。

この記事では、PowerShell インターフェイスを使用して行うタスクに重点を置いて説明します。

この記事には、次の手順が含まれています。

- PowerShell インターフェイスに接続する
- サポート セッションを開始する
- サポート パッケージを作成する
- 証明書のアップロード
- デバイスをリセットする
- デバイス情報を表示する
- コンピューティング ログを取得する
- コンピューティング モジュールの監視とトラブルシューティングを行う

> [!IMPORTANT]
> 現在、Azure Data Box Edge はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>サポート セッションを開始する

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>サポート パッケージを作成する

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>証明書のアップロード

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="view-device-information"></a>デバイス情報を表示する
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>デバイスをリセットする

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>コンピューティング ログを取得する

デバイスにコンピューティング ロールが構成されている場合は、PowerShell インターフェイス経由でコンピューティング ログを取得することもできます。

1. [PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
2. `Get-AzureDataBoxEdgeComputeRoleLogs` を使用してデバイスのコンピューティング ログを取得します。

    このコマンドレットの使用例を次に示します。

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    このコマンドレットで使用されるパラメーターの説明を次に示します。 
    - `Path`:コンピューティング ログ パッケージを作成する共有へのネットワーク パスを指定します。
    - `Credential`:ネットワーク共有のユーザー名とパスワードを指定します。
    - `RoleInstanceName`:このパラメーターには文字列 `IotRole` を指定します。
    - `FullLogCollection`:このパラメーターにより、ログ パッケージにすべてのコンピューティング ログが確実に含まれます。 既定では、ログ パッケージに含まれるものは、ログのサブセットのみです。


## <a name="next-steps"></a>次の手順

- Azure portal で [Azure Data Box Edge](data-box-edge-deploy-prep.md) を配置する。
