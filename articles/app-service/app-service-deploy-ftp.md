---
title: FTP/S を使用した Azure App Service へのアプリのデプロイ | Microsoft Docs
description: FTP または FTPS を使用して Azure App Service にアプリをデプロイする方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S を使用した Azure App Service へのアプリのデプロイ

この記事では、FTP または FTPS を使用して、Web アプリ、モバイル アプリ バックエンド、または API アプリを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法について説明します。

アプリの FTP/S エンドポイントは既にアクティブです。 FTP/S デプロイを有効にするための構成は必要ありません。

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>手順 1: デプロイ資格情報を設定する

アプリの FTP サーバーにアクセスするには、最初にデプロイ資格情報が必要です。 

デプロイ資格情報を設定またはリセットするには、[Azure App Service のデプロイ資格情報](app-service-deployment-credentials.md)に関するページをご覧ください。 このチュートリアルでは、ユーザー レベルの資格情報の使用について説明します。

## <a name="step-2-get-ftp-connection-information"></a>手順 2: FTP の接続情報を取得する

1. [Azure Portal](https://portal.azure.com) でアプリの[リソース ページ](../azure-resource-manager/resource-group-portal.md#manage-resources)を開きます。
2. 左側のナビゲーションで **[概要]** を選択し、**FTP/デプロイ ユーザー**、**FTP ホスト名**、および **FTPS ホスト名**の値をメモします。 

    ![FTP 接続情報](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > FTP サーバーに適切なコンテキストを提供するため、Azure Portal で表示される **[FTP/デプロイ ユーザー]** の値には、アプリ名が含まれます。
    > 左側のナビゲーションで **[プロパティ]** を選択した場合も、これと同じ情報を確認できます。 
    >
    > また、デプロイ パスワードは表示されません。 デプロイ パスワードを忘れた場合は、[手順 1](#step1) に戻って、デプロイ パスワードをリセットする必要があります。
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>手順 3: ファイルを Azure にデプロイする

1. FTP クライアント (例: [Visual Studio](https://www.visualstudio.com/vs/community/)、[FileZilla](https://filezilla-project.org/download.php?type=client)) から、収集した接続情報を使用してアプリに接続します。
3. ファイルとそれぞれのディレクトリ構造を、Azure の [**/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App_Data/Jobs/** ディレクトリ) にコピーします。
4. アプリの URL を参照して、アプリが正しく動作していることを確認します。 

> [!NOTE] 
> [git ベース デプロイ](app-service-deploy-local-git.md)とは異なり、FTP デプロイでは、次のデプロイ自動化はサポートされません。 
>
> - 依存関係の復元 (NuGet、NPM、PIP、Composer の自動化など)
> - .NET バイナリのコンパイル
> - web.config の生成 ([Node.js の例はこちら](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> ローカル コンピューターでこれらの必要なファイルを手動生成した後、アプリと共にデプロイします。
>
>

## <a name="enforce-ftps"></a>FTPS を強制する

セキュリティを強化するには、FTP over SSL のみを許可する必要があります。 FTP デプロイを使用していない場合は、FTP と FTPS の両方を無効にすることもできます。

[Azure Portal](https://portal.azure.com) 上のアプリのリソース ページで、左側のナビゲーションの **[アプリの設定]** を選択します。

暗号化されていない FTP を無効にするには、**[FTPS only]\(FTPS のみ\)** を選択します。 FTP および FTPS の両方を完全に無効にするには、**[無効にする]** を選択します。 完了したら、**[保存]** をクリックします。

![FTP/S の無効化](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>FTP デプロイのトラブルシューティング

- [FTP デプロイをトラブルシューティングするには、どうすればよいですか。](#how-can-i-troubleshoot-ftp-deployment)
- [FTP 接続してコードを発行できません。問題を解決するには、どうすればよいですか。](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [パッシブ モードを使用して Azure App Service で FTP に接続するには、どうすればよいですか。](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP デプロイをトラブルシューティングするには、どうすればよいですか。

FTP デプロイのトラブルシューティングを行うための最初の手順は、デプロイの問題を、ランタイム アプリケーションの問題から切り離すことです。

デプロイの問題が発生すると、通常、アプリにファイルがデプロイされなかったり、誤ったファイルがデプロイされたりします。 これに対処するには、ご自身の FTP デプロイを調査するか、代替デプロイ パス (ソース管理など) を選択します。

ランタイム アプリケーションの問題が発生した場合は、通常、お使いのアプリに正しいファイル セットがデプロイされますが、アプリが適切に動作しません。 これに対処するには、実行時のコードの動作に焦点を当て、特定のエラー パスを調査します。

デプロイの問題かランタイムの問題かを判断するには、「[Deployment vs. runtime issues (デプロイの問題とランタイムの問題)](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)」を参照してください。

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP 接続してコードを発行できません。 問題を解決するには、どうすればよいですか。
入力したホスト名と[資格情報](#step-1--set-deployment-credentials)が正しいかどうかを確認してください。 また、使用しているマシン上の次の FTP ポートが、ファイアウォールによってブロックされていないことも確認します。

- FTP コントロール接続ポート: 21
- FTP データ接続ポート: 989、10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>パッシブ モードを使用して Azure App Service で FTP に接続するには、どうすればよいですか。
Azure App Service では、アクティブ モードとパッシブ モードの両方を使用した接続がサポートされます。 お使いのデプロイ マシンは、通常、(オペレーティング システム内の、またはホーム ネットワークまたはビジネス ネットワークの一部として) ファイアウォールの内側にあるため、パッシブ モードをお勧めします。 [WinSCP ドキュメントに記載された使用例](https://winscp.net/docs/ui_login_connection)をご覧ください。 

## <a name="next-steps"></a>次の手順

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](app-service-deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [Azure App Service のデプロイ資格情報](app-service-deploy-ftp.md)
