---
title: Azure Functions Runtime に到達できない問題のトラブルシューティング方法。
description: 無効なストレージ アカウントのトラブルシューティング方法について説明します。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963888"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>"Functions Runtime に到達できない" 問題のトラブルシューティング方法

この記事は、Azure portal に表示されるときの "Functions ランタイムに到達できません" というエラー メッセージのトラブルシューティングを目的としています。 このエラーが発生すると、ポータルに次のエラー文字列が表示されます。

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

これは、Azure Functions ランタイムが起動できない場合に発生します。 このエラーが発生する最も一般的な理由は、関数アプリでそのストレージ アカウントへのアクセスが失われていることです。 詳しくは、「[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)」をご覧ください。

この記事の残りの部分では、各ケースを特定して解決する方法など、このエラーの次の原因をトラブルシューティングする際に役立ちます。

+ [ストレージ アカウントが削除された](#storage-account-deleted)
+ [ストレージ アカウントのアプリケーション設定が削除された](#storage-account-application-settings-deleted)
+ [ストレージ アカウントの資格情報が無効](#storage-account-credentials-invalid)
+ [ストレージ アカウントにアクセスできない](#storage-account-inaccessible)
+ [日ごとの実行クォータを超過した](#daily-execution-quota-full)
+ [アプリがファイアウォールの内側にある](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>ストレージ アカウントが削除された

すべての関数アプリには、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、Functions は機能しません。

### <a name="how-to-find-your-storage-account"></a>ストレージ アカウントの検索方法

まず、アプリケーションの設定でご自分のストレージ アカウント名を検索します。 `AzureWebJobsStorage` または `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` のいずれかに、接続文字列でラップされたストレージ アカウントの名前が含まれます。 詳細については、[こちらのアプリケーション設定のリファレンス](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)をご覧ください。

Azure portal でご自分のストレージ アカウントを検索し、まだ存在しているかどうかを確認します。 削除されている場合は、ストレージ アカウントを作成し直して、ストレージ接続文字列を置換する必要があります。 関数コードが失われ、もう一度デプロイし直す必要があります。

## <a name="storage-account-application-settings-deleted"></a>ストレージ アカウントのアプリケーション設定が削除された

前の手順で、ストレージ アカウントの接続文字列がなかった場合は、削除されたか上書きされた可能性があります。 アプリの設定の削除は、デプロイ スロットまたは Azure Resource Manager スクリプトを使用して、アプリケーション設定を設定するときに最もよく行われます。

### <a name="required-application-settings"></a>必須のアプリケーションの設定

* Required
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 従量課金プラン (Functions) には必須
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

これらのアプリケーション設定については、[こちら](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)をご覧ください。

### <a name="guidance"></a>ガイダンス

* これらの設定のいずれに対しても、"スロット設定" をオンにしないでください。 デプロイ スロットをスワップすると、関数アプリが中断されます。
* 自動デプロイの一環としてこれらの設定を変更しないようにしてください。
* これらの設定は、作成時に指定して有効にする必要があります。 これらの設定が含まれない自動デプロイでは、関数アプリが実行されなくなります。後から設定を追加しても実行されません。

## <a name="storage-account-credentials-invalid"></a>ストレージ アカウントの資格情報が無効

ストレージ キーを再生成する場合、上記のストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理については、[こちら](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)をご覧ください。

## <a name="storage-account-inaccessible"></a>ストレージ アカウントにアクセスできない

関数アプリは、ストレージ アカウントにアクセスできる必要があります。 ストレージ アカウントへの Functions のアクセスをブロックする一般的な問題は次のとおりです。

+ ストレージ アカウント間のトラフィックを許可するため、正しいネットワーク規則なしで関数アプリが App Service Environment (ASE) にデプロイされた。

+ ストレージ アカウントのファイアウォールが有効になっていて、Functions 間のトラフィックを許可するように構成されていない。 詳細については、[「Azure Storage ファイアウォールおよび仮想ネットワークを構成する」](../storage/common/storage-network-security.md)を参照してください。

## <a name="daily-execution-quota-full"></a>日ごとの実行クォータがいっぱいになった

日ごとの実行クォータが構成されている場合、関数アプリは一時的に無効になり、これにより、多くのポータル コントロールが使用できなくなります。 

+ [Azure portal](https://portal.azure.com) で検証するには、関数アプリで **[プラットフォーム機能]**  >  **[関数アプリの設定]** を開きます。 設定した**日ごとの使用量クォータ**を超えている場合は、次のメッセージが表示されます。

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ この問題を解決するには、日ごとのクォータを削除するか増やし、アプリを再起動します。 それ以外の場合、アプリの実行は次の日までブロックされます。

## <a name="app-is-behind-a-firewall"></a>アプリがファイアウォールの内側にある

関数アプリが[内部で負荷分散された App Service Environment](../app-service/environment/create-ilb-ase.md) 内でホストされ、受信インターネット トラフィックをブロックするように構成されている場合、またはインターネット アクセスをブロックするように構成されている[受信 IP 制限](functions-networking-options.md#inbound-ip-restrictions)がある場合は、Functions Runtime に到達できません。 Azure portal は、実行中のアプリに対して直接呼び出しを行い、関数の一覧を取得します。また、KUDU エンドポイントに対して HTTP 呼び出しを行います。 プラットフォーム レベルの設定は、[`Platform Features`] タブでも使用できます。

ASE の構成を確認するには、ASE が存在するサブネットの NSG に移動し、アプリケーションにアクセスしているコンピューターのパブリック IP からのトラフィックを許可する受信規則を検証します。 また、アプリを実行している仮想ネットワーク、または仮想ネットワークで実行されている仮想マシンから、ポータルを使用することもできます。 [受信規則の構成の詳細については、こちらを参照してください](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>次の手順

関数アプリの監視についての詳細情報:

> [!div class="nextstepaction"]
> [Azure Functions を監視する](functions-monitoring.md)
