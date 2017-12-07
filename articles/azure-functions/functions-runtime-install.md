---
title: "Azure Functions ランタイムのインストール | Microsoft Docs"
description: "Azure Functions ランタイムをインストールする方法"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: b6078ba7b553773294ebbf11949f7d3b53f46f0c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions ランタイム プレビューをインストールする

Azure Functions ランタイム プレビューをインストールする場合は、次の手順に従う必要があります。

1. コンピューターが最小要件を満たしていることを確認します。
1. [Azure Functions ランタイム プレビュー インストーラー](https://aka.ms/azafr)をダウンロードします。
1. Azure Functions ランタイム プレビューをインストールします。
1. Azure Functions ランタイム プレビューの構成を完了します。

## <a name="prerequisites"></a>前提条件

Azure Functions ランタイム プレビューをインストールする前に、次の内容を準備する必要があります。

1. Microsoft Windows Server 2016 または Microsoft Windows 10 Creators Update (Professional または Enterprise Edition) を実行しているコンピューター。
1. ネットワーク上で実行される SQL Server のインスタンス。  必要な最小エディションは SQL Server Express です。

## <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions ランタイム プレビューをインストールする

Azure Functions ランタイム プレビュー インストーラーの手順に従って、Azure Functions ランタイム プレビューの管理ロールと worker ロールをインストールします。  同じコンピューターに管理ロールと worker ロールをインストールすることができます。  ただし、多くの関数を追加すると、複数の worker ロールに関数を拡張できるように、追加のマシン上にさらに worker ロールをデプロイする必要があります。

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>同じコンピューターに管理ロールと worker ロールをインストールする

1. Azure Functions ランタイム プレビュー インストーラーを実行します。

    ![Azure Functions ランタイム プレビュー インストーラー][1]

1. **[次へ]** をクリックします。
1. **使用許諾契約書**の条項を読み終わったら、**チェック ボックスをオン**にして条項に同意し、**[次へ]** をクリックして先に進みます。
1. このコンピューターにインストールするロールとして **[Functions Management Role]\(Functions 管理ロール\)** と **[Functions Worker Role]\(Functions worker ロール\)** のどちらか一方または両方を選び、**[次へ]** をクリックします。

    ![Azure Functions ランタイム プレビュー インストーラー - ロールの選択][3]

    > [!NOTE]
    > **Functions worker ロール**は、他の多くのコンピューターにインストールできます。 そのためには、この手順に従い、インストーラーで **[Functions Worker Role]\(Functions worker ロール\)** だけを選びます。

1. **[次へ]** をクリックして、**Azure Functions ランタイム セットアップ ウィザード**でコンピューターへのインストール プロセスを開始します。
1. 完了すると、セットアップ ウィザードによって **Azure Functions ランタイム**構成ツールが起動されます。

    ![Azure Functions ランタイム プレビュー インストーラーの完了][5]

    > [!NOTE]
    > **Windows 10** にインストールしている場合、**コンテナー**機能が前もって有効にされていないときは、インストールを完了するためにコンピューターを再起動するように **Azure Functions ランタイム セットアップ**から求められます。

## <a name="configure-the-azure-functions-runtime"></a>Azure Functions ランタイムを構成する

Azure Functions ランタイムのインストールを完了するには、構成を完了する必要があります。

1. **Azure Functions ランタイム**構成ツールに、コンピューターにインストールされるロールが表示されます。

    ![Azure Functions ランタイム プレビュー構成ツール][6]

1. **[データベース]** タブをクリックし、SQL Server インスタンスの接続の詳細を入力して、**[適用]** をクリックします。  これは、Azure Functions ランタイムでランタイムをサポートするデータベースを作成するために必要です。
    
    ![Azure Functions ランタイム プレビュー データベース構成][7]

1. **[資格情報]** タブをクリックします。この画面で、すべての Azure Functions をホストするために、FileShare と使用する目的で新しい資格情報を 2 つ作成する必要があります。  **ファイル共有の所有者**および**ファイル共有のユーザー**の **[ユーザー名]** と **[パスワード]** の組み合わせを指定し、**[適用]** をクリックします。

    ![Azure Functions ランタイム プレビュー資格情報][8]

1. **[ファイルの共有]** タブをクリックします。この画面では、**ファイルの共有**の場所の詳細を指定する必要があります。  そのためには、自分用に作成するか、既存のファイル共有を使用して、**[適用]** をクリックします。  新しいファイル共有の場所を選択した場合は、Azure Functions ランタイムで使用するディレクトリを指定する必要があります。
    
    ![Azure Functions ランタイム プレビュー ファイル共有][9]

1. **[IIS]** タブをクリックします。このタブには、Azure Functions ランタイム構成ツールで作成される IIS の Web サイトの詳細が表示されます。  **[適用]** をクリックして完了します。

    ![Azure Functions ランタイム プレビュー IIS][10]

1. **[サービス]** タブをクリックします。このタブには、Azure Functions ランタイム構成ツールのサービスの状態が表示されます。  初期構成後、**Azure Functions ホスト アクティブ化サービス**が実行されていない場合は、**[サービスの開始]** をクリックします。

    ![Azure Functions ランタイム プレビュー構成の完了][11]

1. 最後に、**Azure Functions ランタイム ポータル**を `https://<machinename>/` として参照します。

    ![Azure Functions ランタイム プレビュー ポータル][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
