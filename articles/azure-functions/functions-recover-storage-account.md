---
title: Azure Functions ランタイムに到達できないエラーのトラブルシューティング
description: 無効なストレージ アカウントのトラブルシューティング方法について説明します。
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063783"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>"Azure Functions ランタイムに到達できない" エラーのトラブルシューティング

この記事は、Azure portal に表示される次のエラー文字列をトラブルシューティングする場合に役立ちます。

> "エラー: Azure Functions ランタイムに到達できません。 ストレージ構成の詳細については、ここをクリックしてください。"

この問題は、Azure Functions ランタイムが起動できない場合に発生します。 この問題が発生する最も一般的な理由は、関数アプリでそのストレージ アカウントへのアクセスが失われていることです。 詳細については、「[ストレージ アカウントの要件](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)」をご覧ください。

この記事の残りの部分では、各ケースを特定して解決する方法など、このエラーの次の原因をトラブルシューティングする際に役立ちます。

## <a name="storage-account-was-deleted"></a>ストレージ アカウントが削除された

すべての関数アプリには、操作するためのストレージ アカウントが必要です。 そのアカウントが削除されると、関数は機能しません。

まず、アプリケーションの設定でご自分のストレージ アカウント名を検索します。 `AzureWebJobsStorage` または `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` のいずれかに、接続文字列でラップされたストレージ アカウントの名前が含まれています。 詳細については、「[Azure Functions のアプリケーション設定のリファレンス](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)」をご覧ください。

Azure portal でご自分のストレージ アカウントを検索して、まだ存在するかどうかを確認します。 削除されている場合は、ストレージ アカウントを作成し直して、ストレージ接続文字列を置き換えます。 関数コードが失われるため、もう一度デプロイする必要があります。

## <a name="storage-account-application-settings-were-deleted"></a>ストレージ アカウントのアプリケーション設定が削除された

前の手順で、ストレージ アカウントの接続文字列が見つからない場合は、削除されたか上書きされた可能性があります。 アプリケーションの設定の削除は、デプロイ スロットまたは Azure Resource Manager スクリプトを使用してアプリケーションの設定を行うときに最もよく発生します。

### <a name="required-application-settings"></a>必須のアプリケーションの設定

* 必須:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 従量課金プラン関数には必須:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

詳細については、「[Azure Functions のアプリケーション設定のリファレンス](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)」をご覧ください。

### <a name="guidance"></a>ガイダンス

* これらの設定のいずれに対しても、"スロット設定" をオンにしないでください。 デプロイ スロットをスワップすると、関数アプリが中断されます。
* 自動デプロイの一環としてこれらの設定を変更しないようにしてください。
* これらの設定は、作成時に指定して有効にする必要があります。 これらの設定が含まれない自動デプロイでは、関数アプリが実行されなくなります。後から設定を追加しても実行されません。

## <a name="storage-account-credentials-are-invalid"></a>ストレージ アカウントの資格情報が無効

ストレージ キーを再生成する場合は、前述のストレージ アカウント接続文字列を更新する必要があります。 ストレージ キーの管理の詳細については、「[Azure Storage アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)」をご覧ください。

## <a name="storage-account-is-inaccessible"></a>ストレージ アカウントにアクセスできない

関数アプリは、ストレージ アカウントにアクセスできる必要があります。 ストレージ アカウントへの関数アプリのアクセスをブロックする一般的な問題は次のとおりです。

* ストレージ アカウント間のトラフィックを許可するため、正しいネットワーク規則なしで関数アプリが App Service Environment にデプロイされた。

* ストレージ アカウントのファイアウォールが有効になっていて、Functions 間のトラフィックを許可するように構成されていない。 詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事を参照してください。

## <a name="daily-execution-quota-is-full"></a>日ごとの実行クォータがいっぱいである

日ごとの実行クォータが構成されている場合、関数アプリは一時的に無効になり、これにより、多くのポータル コントロールが使用できなくなります。 

[Azure portal](https://portal.azure.com) でクォータを確認するには、関数アプリで **[プラットフォーム機能]**  >  **[関数アプリの設定]** を選択します。 設定した**日ごとの使用量クォータ**を超えている場合は、次のメッセージが表示されます。

  > "関数アプリは日ごとの使用量クォータに達したため、次の 24 時間の時間枠まで停止しています。"

この問題を解決するには、日ごとのクォータを削除するか増やしてから、アプリを再起動します。 そうしない場合は、アプリの実行が翌日までブロックされます。

## <a name="app-is-behind-a-firewall"></a>アプリがファイアウォールの内側にある

次のいずれかの理由により、関数ランタイムに到達できない可能性があります。

* 関数アプリが[内部で負荷分散された App Service Environment](../app-service/environment/create-ilb-ase.md) 内でホストされ、受信インターネット トラフィックをブロックするように構成されている。

* 関数アプリに、インターネット アクセスをブロックするように構成されている[受信 IP 制限](functions-networking-options.md#inbound-ip-restrictions)が設けられている。 

Azure portal は、実行中のアプリに対して直接呼び出しを行い、関数の一覧を取得します。また、Kudu エンドポイントに対して HTTP 呼び出しを行います。 プラットフォームレベルの設定は、 **[プラットフォーム機能]** タブで引き続き使用できます。

App Service Environment 構成を確認するには、次の操作を行います。
1. App Service Environment が存在するサブネットのネットワーク セキュリティ グループ (NSG) に移動します。
1. アプリケーションにアクセスしているコンピューターのパブリック IP から送信されるトラフィックを許可する受信規則を検証します。 
   
また、アプリを実行している仮想ネットワーク、または仮想ネットワークで実行されている仮想マシンに接続されているコンピューターからポータルを使用することもできます。 

受信規則の構成の詳細については、「[App Service Environment のネットワークの考慮事項](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)」の「ネットワーク セキュリティ グループ」セクションをご覧ください。

## <a name="next-steps"></a>次のステップ

関数アプリの監視についての詳細情報:

> [!div class="nextstepaction"]
> [Azure Functions を監視する](functions-monitoring.md)
