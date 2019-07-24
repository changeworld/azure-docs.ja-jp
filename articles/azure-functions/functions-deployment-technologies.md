---
title: Azure Functions のデプロイ テクノロジ | Microsoft Docs
description: Azure Functions にコードをデプロイするさまざまな方法について詳しく説明します。
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 118daf02ab59646f2926071763aa4d7e97846e04
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508221"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions のデプロイ テクノロジ

Azure Functions プロジェクト コードを Azure にデプロイするために使用できる各種のテクノロジがあります。 この記事では、これらのテクノロジの包括的な一覧を提供し、どのテクノロジをどの種類の関数に対して使用できるか示し、各手法を使用する場合に実際にどのようなことが起こるかについて説明し、各種シナリオで使用する最適な手法について推奨を提示します。 Azure Functions へのデプロイをサポートする各種ツールは、それらのコンテキストに基づいて適切なテクノロジとなるように調整されています。

## <a name="deployment-technology-availability"></a>デプロイ テクノロジの利用可否

> [!IMPORTANT]
> Azure Functions は、クロス プラットフォームのローカル開発と、次の 2 つのオペレーティング システムでのホスティングをサポートしています。Windows と Linux。 現在利用できるホスティング プランは [Consumption](functions-scale.md#consumption-plan)、[Premium](functions-scale.md#premium-plan)、および [dedicated (App Service)](functions-scale.md#app-service-plan) の 3 つで、それぞれ動作が異なります。 各種の Azure Functions に対してすべてのデプロイ テクノロジが使用できるわけではありません。

| デプロイ テクノロジ | Windows Consumption | Windows Premium (プレビュー) | Windows Dedicated  | Linux Consumption (プレビュー) | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| 外部パッケージ URL<sup>1</sup> |✔|✔|✔|✔|✔|
| ZIP デプロイ |✔|✔|✔| |✔|
| Docker コンテナー | | | | |✔|
| Web デプロイ |✔|✔|✔| | |
| ソース管理 |✔|✔|✔| |✔|
| ローカル Git<sup>1</sup> |✔|✔|✔| |✔|
| クラウドの同期<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| ポータルでの編集 |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> [トリガーの手動同期](#trigger-syncing)が必要なデプロイ テクノロジ。
<sup>2</sup> ポータルでの編集は、Dedicated プランを使用する Linux での関数の HTTP およびタイマー トリガーに対してのみ使用できます。

## <a name="key-concepts"></a>主要な概念

続行する前に、いくつかの主要概念について学習することが重要です。これらの概念は、Azure Functions でデプロイがどのように動作するか理解するために必要です。

### <a name="trigger-syncing"></a>トリガーの同期

トリガーのいずれかを変更する場合、関数インフラストラクチャにそれらの変更を認識させる必要があります。 この同期は、多くのデプロイ テクノロジでは自動的に実行されます。 ただし、場合によっては、トリガーを手動で同期する必要があります。 外部パッケージ URL、ローカル Git、クラウドの同期、または FTP を使用して更新をデプロイする場合は、必ずトリガーを手動で同期する必要があります。 次の 3 つの方法のいずれかでトリガーを同期できます。

* Azure portal で関数アプリを再起動する。
* [マスター キー](functions-bindings-http-webhook.md#authorization-keys)を使用して HTTP POST 要求を `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` に送信する。
* HTTP POST 要求を `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` に送信する。 プレースホルダーは、ご使用のサブスクリプション ID、リソース グループ名、および関数アプリの名前に置き換えてください。

## <a name="deployment-technology-details"></a>デプロイ テクノロジの詳細  

Azure Functions では、次のデプロイ方法がサポートされています。

### <a name="external-package-url"></a>外部パッケージ URL

関数アプリが含まれるリモート パッケージ (.zip) ファイルを参照できます。 このファイルは、指定の URL からダウンロードされます。アプリは [Run-From-Package](run-functions-from-deployment-package.md) モードで実行されます。

>__使用方法:__ アプリケーションの設定に `WEBSITE_RUN_FROM_PACKAGE` を追加します。 この設定では、値として URL (実行する特定のパッケージ ファイルの場所) を指定する必要があります。 [ポータルで](functions-how-to-use-azure-function-app-settings.md#settings)、または [Azure CLI を使用して](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)設定を追加できます。 Azure Blob Storage を使用する場合、関数にパッケージへのアクセス権を付与するために、[Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) を備えたプライベート コンテナーを使用する必要があります。 アプリケーションは、再起動時には常にコンテンツのコピーをフェッチします。したがって、アプリケーションの有効期間中、参照が有効でなければなりません。

>__いつ使用するか:__ これは、Consumption プラン (プレビュー) の Linux で実行される Azure Functions に対してサポートされる唯一のデプロイ方法です。 関数アプリが参照しているパッケージ ファイルを更新する場合、Azure にアプリケーションが変更されたことを通知するために、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="zip-deploy"></a>ZIP デプロイ

関数アプリが含まれる ZIP ファイルを Azure にプッシュできます。 必要に応じて、アプリが [Run-From-Package](run-functions-from-deployment-package.md) モードで起動するよう指定することもできます。

>__使用方法:__ 任意のクライアント ツール ([VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)、[Visual Studio](functions-develop-vs.md#publish-to-azure)、または [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)) を使用してデプロイします。 ZIP ファイルを関数アプリに手動でデプロイするには、[ZIP ファイルまたは URL からのデプロイ](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)に関する記事の指示に従います。
>
>また、ZIP デプロイを通じてデプロイする場合、ユーザーは、`WEBSITE_RUN_FROM_PACKAGE` アプリケーション設定値を `1` として設定することで、[Run-From-Package](run-functions-from-deployment-package.md) モードでアプリが実行されるよう指定できます。 このオプションは推奨されており、このオプションによりアプリケーションの読み込み時間が短縮されます。 これは、上記のクライアント ツールでは既定で実行されます。

>__いつ使用するか:__ これは、 Windows で実行される Azure Functions と Linux で実行される Azure Functions (Dedicated プラン) に対して推奨されるデプロイ テクノロジです。

### <a name="docker-container"></a>Docker コンテナー

関数アプリが含まれる Linux コンテナー イメージをデプロイします。

>__使用方法:__ Dedicated プランで Linux 関数アプリを作成し、実行元のコンテナー イメージを指定します。 次の 2 つの方法で行います。
>
>* Azure portal を使用して、App Service プランで Linux 関数アプリを作成します。 **パブリッシュ**用の **Docker イメージ**を選択し、イメージがホストされる場所を指定して、コンテナーを構成します。
>* Azure CLI を使用して、App Service プランで Linux 関数アプリを作成します。 作成方法については、「[カスタム イメージを使用して Linux で関数を作成する](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)」をご覧ください。
>
>カスタム コンテナーを使用して既存のアプリにデプロイするには、[Azure Functions Core Tools](functions-run-local.md) の [`func deploy`](functions-run-local.md#publish) コマンドを使用します。

>__いつ使用するか:__ このオプションは、関数アプリが実行される Linux 環境をより詳細に制御する必要がある場合に使用します。 このデプロイ メカニズムは、App Service プランの Linux で実行される関数に対してのみ使用できます。

### <a name="web-deploy-msdeploy"></a>Web デプロイ (MSDeploy)

Azure の Windows で実行される関数アプリを含む Windows アプリケーションをパッケージ化し、IIS サーバーにデプロイします。

>__使用方法:__ [Visual Studio tools for Azure Functions](functions-create-your-first-function-visual-studio.md) を使用し、[`Run from package file (recommended)`] ボックスをオフにします。
>
> また、[Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) をダウンロードし、`MSDeploy.exe` を直接呼び出すこともできます。

>__いつ使用するか:__ このデプロイ テクノロジはサポートされており、問題はありませんが、現在推奨されているメカニズムは、[[パッケージから実行する] が有効化された ZIP デプロイ](#zip-deploy)です。 詳細については、[Visual Studio デプロイ ガイド](functions-develop-vs.md#publish-to-azure)に関する記事をご覧ください。

### <a name="source-control"></a>ソース管理

関数アプリを Git リポジトリに接続して、そのリポジトリ内のコードへの更新によってデプロイがトリガーされるようにすることができます。 詳細については、「[Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)」をご覧ください。

>__使用方法:__ Azure Functions ポータルでデプロイ センターを使用して、ソース管理からのパブリッシュを設定します。 詳細については、「[Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)」を参照してください。

>__いつ使用するか:__ 関数アプリに関してチームで共同作業を行う場合、ソース管理を使用することがベスト プラクティスです。また、これは、より洗練されたデプロイ パイプラインを可能にする優れたオプションです。

### <a name="local-git"></a>ローカル Git

Git を使用して、ローカル コンピューターから Azure Functions にコードをプッシュできます。

>__使用方法:__ 「[Azure App Service へのローカル Git デプロイ](../app-service/deploy-local-git.md)」の指示に従ってください。

>__いつ使用するか:__ 一般に、他のデプロイ方法が推奨されます。 ローカル Git からパブリッシュする場合、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="cloud-sync"></a>クラウドの同期

Dropbox および OneDrive から Azure Functions にコンテンツを同期できます。

>__使用方法:__ 「[クラウド フォルダーからのコンテンツを同期する](../app-service/deploy-content-sync.md)」の指示に従ってください。

>__いつ使用するか:__ 一般に、他のデプロイ方法が推奨されます。 クラウドの同期を使用してパブリッシュする場合、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="ftp"></a>FTP

ファイルを Azure Functions に直接転送できます。

>__使用方法:__ 「[FTP/S を使用してコンテンツをデプロイする](../app-service/deploy-ftp.md)」の指示に従ってください。

>__いつ使用するか:__ 一般に、他のデプロイ方法が推奨されます。 FTP を使用してパブリッシュする場合、[トリガーを手動で同期](#trigger-syncing)する必要があります。

### <a name="portal-editing"></a>ポータルでの編集

ポータル ベースのエディターを使用して、関数アプリのファイルを直接編集できます (基本的には、 **[保存]** をクリックするたびにデプロイされます)。

>__使用方法:__ Azure portal で関数を編集できるようにするために、[ポータルで関数を作成](functions-create-first-azure-function.md)しておく必要があります。 他のデプロイ方法を使用すると、関数が読み取り専用になり、ポータルで編集することはできなくなり、単一の信頼できるソースとして保持されます。 Azure portal を使用してファイルを編集できる状態に戻すには、編集モードを `Read/Write` に手動で戻し、デプロイ関連のアプリケーション設定 (`WEBSITE_RUN_FROM_PACKAGE` など) をすべて削除します。 

>__いつ使用するか:__ ポータルは、Azure Functions の使用を開始する優れた方法ですが、より集中的な開発作業を行うには、クライアント ツールを使用することをお勧めします。
>
>* [VS Code を使用して開始する](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools を使用して開始する](functions-run-local.md)
>* [Visual Studio を使用して開始する](functions-create-your-first-function-visual-studio.md)

次の表に、ポータルでの編集がサポートされるオペレーティング システムと言語を示します。

| | Windows Consumption | Windows Premium (プレビュー) | Windows Dedicated | Linux Consumption (プレビュー) | Linux Dedicated |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# スクリプト |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js)JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (プレビュー) | | | | | |
| PowerShell (プレビュー) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> ポータルでの編集は、Dedicated プランを使用する Linux の関数の HTTP およびタイマー トリガーに対してのみ使用できます。

## <a name="deployment-slots"></a>デプロイ スロット

関数アプリを Azure にデプロイする場合、運用環境に直接デプロイする代わりに、個別のデプロイ スロットにデプロイできます。 デプロイ スロットの詳細については、[Azure App Service スロットのドキュメント](../app-service/deploy-staging-slots.md)に関するページをご覧ください。

### <a name="deployment-slots-levels-of-support"></a>デプロイ スロットのサポートのレベル

次の 2 つのレベルのサポートがあります。

* _一般公開 (GA)_ - 完全にサポートされ、運用環境用に承認されています。
* _プレビュー_ - まだサポートされていませんが、今後 GA 状態に達すると想定されています。

| OS/ホスティング プラン | サポートのレベル |
| --------------- | ------ |
| Windows Consumption | プレビュー |
| Windows Premium (プレビュー) | プレビュー |
| Windows Dedicated | 一般公開 |
| Linux Consumption | サポートされていません |
| Linux Dedicated | 一般公開 |

## <a name="next-steps"></a>次の手順

次の記事で、関数アプリのデプロイについて詳しい情報を確認してください。 

+ [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)
+ [Azure DevOps を使用した継続的デリバリー](functions-how-to-azure-devops.md)
+ [Azure Functions の ZIP デプロイ](deployment-zip-push.md)
+ [Azure Functions をパッケージ ファイルから実行する](run-functions-from-deployment-package.md)
+ [Azure Functions の関数アプリのリソース デプロイを自動化](functions-infrastructure-as-code.md)
