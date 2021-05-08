---
title: 仮想ネットワークで Azure Functions を構成する方法
description: Azure Functions の特定の仮想ネットワーク タスクを実行する方法を示す記事。
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718433"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>仮想ネットワークで Azure Functions を構成する方法

この記事では、仮想ネットワークに接続してそこで実行されるように関数アプリを構成するタスクを実行する方法について説明します。 Azure Functions とネットワークの詳細については、「[Azure Functions のネットワーク オプション](functions-networking-options.md)」を参照してください。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>お使いのストレージ アカウントを仮想ネットワークに制限する 

関数アプリを作成するときは、BLOB、Queue、および Table Storage をサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 このストレージ アカウントは、サービス エンドポイントまたはプライベート エンドポイントで保護されているものに置き換えることができます。 

> [!NOTE]  
> この機能は、現在、専用 (App Service) プランのすべての Windows 仮想ネットワーク対応 SKU と、Premium プランで有効です。 従量課金プランはサポートされていません。 

プライベート ネットワークに制限されたストレージ アカウントを使用して関数を設定するには、次のようにします。

1. サービス エンドポイントが有効になっていないストレージ アカウントを使用して関数を作成します。

1. ご使用の仮想ネットワークに接続するように関数を構成します。

1. 別のストレージ アカウントを作成または構成します。  これがサービス エンドポイントで保護され、関数に接続されるストレージ アカウントになります。

1. セキュリティで保護されたストレージ アカウントに[ファイル共有を作成](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)します。

1. ストレージ アカウントのサービス エンドポイントまたはプライベート エンドポイントを有効にします。  
    * プライベート エンドポイント接続を使用する場合、ストレージ アカウントには、`file` と `blob` のサブリソース用のプライベート エンドポイントが必要です。  Durable Functions のような特定の機能を使用する場合は、プライベート エンドポイント接続を介して `queue` と `table` にアクセスできる必要もあります。
    * サービス エンドポイントを使用する場合は、ストレージ アカウントに対して関数アプリ専用のサブネットを有効にします。

1. 関数アプリのストレージ アカウントから、セキュリティで保護されたストレージ アカウントとファイル共有に、ファイルと BLOB の内容をコピーします。

1. このストレージ アカウントの接続文字列をコピーします。

1. 関数アプリの **[構成]** の下の **[アプリケーションの設定]** を次のように更新します。

    | 設定名 | 値 | 解説 |
    |----|----|----|
    | `AzureWebJobsStorage`| ストレージ接続文字列 | これは、セキュリティで保護されたストレージ アカウントの接続文字列です。 |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  ストレージ接続文字列 | これは、セキュリティで保護されたストレージ アカウントの接続文字列です。 |
    | `WEBSITE_CONTENTSHARE` | ファイル共有 | プロジェクト配置ファイルが存在する、セキュリティで保護されたストレージ アカウントに作成されたファイル共有の名前。 |
    | `WEBSITE_CONTENTOVERVNET` | 1 | 新しい設定 |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | すべての送信トラフィックが強制的に仮想ネットワーク経由になります。 ストレージ アカウントでプライベート エンドポイント接続を使用している場合は必須です。 |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | アプリによって使用される DNS サーバー。 ストレージ アカウントでプライベート エンドポイント接続を使用している場合は必須です。 |

1. **[保存]** を選択して、アプリケーション設定を保存します。 アプリの設定を変更すると、アプリが再起動されます。  

関数アプリが再起動すると、セキュリティで保護されたストレージ アカウントに接続されるようになります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のネットワーク オプション](functions-networking-options.md)

