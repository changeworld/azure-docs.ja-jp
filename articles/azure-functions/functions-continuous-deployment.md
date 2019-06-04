---
title: Azure Functions の継続的なデプロイ | Microsoft Docs
description: Azure App Service の継続的なデプロイ機能を使用して、関数を発行します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943894"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions の継続的なデプロイ
Azure Functions を利用すると、継続的インテグレーションを使用して、関数アプリを簡単にデプロイできます。 Functions は、主要なコード リポジトリおよびデプロイ ソースと統合します。 この統合により、関数コードの更新がこれらのサービスのいずれかによって行われるワークフローで、Azure へのデプロイをトリガーできるようになります。 Azure Functions を初めて使う場合は、最初に「[Azure Functions の概要](functions-overview.md)」をご覧ください。

複数の頻繁に発生する投稿を統合する場合、継続的なデプロイはプロジェクトに最適なオプションとなります。 また、関数コードのソース管理を維持することもできます。 Azure Functions では次のデプロイ ソースがサポートされています。

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* 外部リポジトリ (Git または Mercurial)
* [Git ローカル リポジトリ](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

デプロイは関数アプリごとに構成されます。 継続的なデプロイが有効になると、 ポータル内の関数コードへのアクセスは、 *読み取り専用*に設定されます。

## <a name="requirements-for-continuous-deployment"></a>継続的なデプロイの要件

継続的なデプロイを設定する前に、デプロイ ソースを構成し、そのデプロイ ソースに関数コードを追加する必要があります。 関数アプリのデプロイでは、各関数は名前付きサブディレクトリに存在します。その場合のディレクトリ名は、関数の名前です。  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure DevOps からデプロイできるようにするには、まず、Azure サブスクリプションに Azure DevOps 組織をリンクする必要があります。 詳しくは、「[Set up billing for your Azure DevOps organization](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal)」\(Azure DevOps 組織の課金の設定\) をご覧ください。

## <a name="set-up-continuous-deployment"></a>Azure App Service での GIT による継続的なデプロイ
既存の関数アプリの継続的なデプロイを構成するには、次の手順に従います。 この手順は、GitHub リポジトリとの統合を示しますが、Azure DevOps またはその他のデプロイ サービスに対しても同様の手順が適用されます。

1. [Azure portal](https://portal.azure.com) の関数アプリで、 **[プラットフォーム機能]**  >  **[デプロイ オプション]** を選択します。 
   
    ![デプロイ オプションを開くための選択](./media/functions-continuous-deployment/setup-deployment.png)
 
1. **[デプロイ]** ブレードで **[セットアップ]** を選択します。
 
    ![[デプロイ] ブレード](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. **[展開元]** ブレードで、 **[ソースの選択]** を選択します。 選択したデプロイ ソースの情報を入力し、 **[OK]** を選択します。
   
    ![デプロイ ソースの選択](./media/functions-continuous-deployment/choose-deployment-source.png)

継続的なデプロイを設定した後は、デプロイ ソース内のすべてのファイル変更が関数アプリにコピーされ、完全なサイトのデプロイがトリガーされます。 ソース内のファイルが更新されると、サイトは再デプロイされます。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

一般的なデプロイ シナリオには、ステージング デプロイの作成と、継続的なデプロイへの既存の関数の移動が含まれます。

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>ステージング環境のデプロイの作成

関数アプリではデプロイ スロットはまだサポートされていません。 ただし、継続的インテグレーションを使用して、個別のステージング環境と運用環境のデプロイを管理できます。

ステージング環境のデプロイを構成し、使用するプロセスは、一般的には次のようになります。

1. サブスクリプションで運用コード用とステージング用の 2 つの関数アプリを作成します。 

1. 既にお持ちでない場合は、デプロイ ソースを作成します。 この例では、[GitHub] を使用します。

1. 運用環境の関数アプリでは、前述の「[継続的なデプロイの設定](#set-up-continuous-deployment)」の手順を完了し、GitHub リポジトリのマスター分岐にデプロイ分岐を設定します。
   
    ![デプロイ分岐を選択するオプション](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. ステージング環境の関数アプリでステップ 3 を繰り返しますが、代わりに GitHub リポジトリ内のステージング分岐を選択します。 デプロイ ソースが分岐をサポートしていない場合は、別のフォルダーを使用します。
    
1. ステージング環境の分岐またはフォルダーのコードに対して更新を行ってから、ステージング デプロイでそれらの変更が反映されていることを確認します。

1. テストした後、マージはステージング環境の分岐からマスター分岐に変更されます。 このマージにより、運用環境の関数アプリへのデプロイがトリガーされます。 デプロイ ソースが分岐をサポートしていない場合は、ステージング環境のフォルダーから運用環境のフォルダー内のファイルを上書きします。

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>継続的なデプロイへの既存の関数の移動
ポータル内で作成および保持した既存の関数がある場合は、FTP またはローカルの Git リポジトリを使用して、関数コードをダウンロードする必要があります。その後、上記で説明したように継続的なデプロイを設定することができます。 これは関数アプリの Azure App Service の設定で行うことができます。 ファイルをダウンロードした後は、選択した継続的なデプロイ ソースにそれらをアップロードできます。

> [!NOTE]
> 継続的インテグレーションを構成した後は、Functions のポータルでソース ファイルを編集することはできなくなります。

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>デプロイ資格情報の構成
FTP またはローカル Git リポジトリを使用して関数アプリからファイルをダウンロードする前に、サイトにアクセスするための資格情報を構成する必要があります。 資格情報は関数アプリ レベルで設定されます。 以下の手順に従って、Azure Portal でデプロイ資格情報を設定します。

1. [Azure portal](https://portal.azure.com) の関数アプリで、 **[プラットフォーム機能]**  >  **[デプロイ資格情報]** を選択します。
   
1. ユーザー名とパスワードを入力し、 **[保存]** を選択します。 

   ![ローカル デプロイ資格情報を設定するための選択](./media/functions-continuous-deployment/setup-deployment-credentials.png)

これでこれらの資格情報を使用して、FTP または組み込みの Git リポジトリから関数アプリにアクセスできます。

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>FTP を使用してファイルをダウンロードする

1. [Azure portal](https://portal.azure.com) の関数アプリで、 **[プラットフォーム機能]**  >  **[プロパティ]** を選択します。 次に、 **[FTP/デプロイ ユーザー]** 、 **[FTP ホスト名]** 、および **[FTPS ホスト名]** の値をコピーします。  

   **[FTP/デプロイ ユーザー]** には、FTP サーバーに適切なコンテキストを提供するために、ポータルに表示されているとおり、アプリ名を含めて入力してください。
   
   ![デプロイ情報を取得するための選択](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. FTP クライアントから、収集した接続情報を使用してアプリに接続し、関数のソース ファイルをダウンロードします。

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>ローカルの Git リポジトリを使用したファイルのダウンロード

1. [Azure portal](https://portal.azure.com) の関数アプリで、 **[プラットフォーム機能]**  >  **[デプロイ オプション]** を選択します。 
   
    ![デプロイ オプションを開くための選択](./media/functions-continuous-deployment/setup-deployment.png)
 
1. 次に、 **[デプロイ]** ブレードで **[セットアップ]** を選択します。
 
    ![[デプロイ] ブレード](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. **[展開元]** ブレードで、 **[ローカル Git リポジトリ]**  >  **[OK]** を選択します。

1. **[プラットフォーム機能]** で、 **[プロパティ]** を選択し、Git URL の値をメモします。 
   
    ![Git URL を取得するための選択](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Git 対応のコマンド プロンプトまたはお気に入りの Git ツールを使用して、ローカル コンピューターにリポジトリを複製します。 Git clone コマンドは、次のようになります。
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. 次の例のように、関数アプリからローカル コンピューター上のクローンにファイルをフェッチします。
   
        git pull origin master
   
    要求された場合は、[構成されたデプロイ資格情報](#credentials)を入力します。  

[GitHub]: https://github.com/

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティス](functions-best-practices.md)
