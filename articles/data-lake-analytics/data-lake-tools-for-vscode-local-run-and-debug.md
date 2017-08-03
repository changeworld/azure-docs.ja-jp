---
title: "Azure Data Lake Tools - Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code を使用してローカル実行とローカル デバッグを行う方法について説明します。"
Keywords: "VScode、Azure Data Lake Tools、ローカル実行、ローカル デバッグ、ストレージ ファイルのプレビュー、ストレージ パスにアップロード"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ

## <a name="prerequisites"></a>前提条件
- Azure Data Lake Tool for Visual Studio Code。 詳しくは、「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」をご覧ください。
- Visual Studio Code 用の C# (U-SQL ローカル デバッグを実行する場合)。

   ![Data Lake Tools for Visual Studio Code による vscodeCsharp のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > 現在、U-SQL のローカル実行およびデバッグ機能は Windows ユーザーのみをサポートします。 


## <a name="set-up-u-sql-local-run-environment"></a>U-SQL ローカル実行環境の設定

1. **CTRL + SHIFT + P** キーを押してコマンド パレットを開き、「**ADL: Download LocalRun Dependency**」と入力してパッケージをダウンロードします。  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. 以下の出力パネルに表示されるパスから依存関係パッケージを探し、Build Tools をインストールして Win10SDK 10240 をインストールします。 For example:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- Build Tools のインストール: ウィザードの指示に従ってインストールします。   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- Win10SDK 10240 のインストール: インストールの指示に従ってインストールします。  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. 環境変数の設定、**SCOPE_CPP_SDK** 環境変数を次のように設定します。  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
OS を再起動して環境変数の設定を有効にしてください。  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>ローカル実行サービスを開始してローカル アカウントに U-SQL ジョブを送信する 
初めてのユーザーについては、まだの場合は **ADL: Download Localrun Dependency** パッケージが要求されます。
1. **CTRL + SHIFT + P** キーを押してコマンド パレットを開き、「**ADL: Start Local Run Service**」と入力します。
2. 初回の場合は、EULA に同意します。 

   ![EULA に同意する ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. cmd コンソールが表示されます。 初回のユーザーについては、3 と入力し、データ入力用と出力用にローカル フォルダーのパスを入力する必要があります。 その他のオプションについては、既定値を使用できます。 

   ![Data Lake Tools for Visual Studio Code のローカル実行 cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. CTRL + SHIFT + P キーを押してコマンド パレットを開き、「**ADL: Submit Job**」と入力して **[Local]** を選択し、ローカル アカウントにジョブを送信します。

   ![Data Lake Tools for Visual Studio Code のローカルの選択](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. ジョブを送信した後、出力ウィンドウで jobUrl をクリックすることで送信の詳細を確認できます。 また、ジョブ送信の状態は、CMD コンソールで 7 と入力して詳細を確認することもできます。

   ![Data Lake Tools for Visual Studio Code ローカル実行の出力](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code ローカル実行の cmd ステータス](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>U-SQL ジョブのローカル デバッグを開始する  
初めてのユーザーについては、まだの場合は **ADL: Download Localrun Dependency** パッケージが要求されます。
  
1. **CTRL + SHIFT + P** キーを押してコマンド パレットを開き、「**ADL: Start Local Run Service**」と入力します。 cmd ウィンドウが表示されます。 **DataRoot** が設定されていることを確認します。
3. C# コードビハインドにブレークポイントを設定します。
4. スクリプト エディターに戻り、**CTRL + SHIFT + P** キーを押してコマンド パレットを開き、「**Local Debug**」と入力してローカル デバッグ サービスを開始します。

![Data Lake Tools for Visual Studio Code のローカル実行結果](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>次のステップ
- Data Lake Tools for Visual Studio Code の使用について詳しくは、「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」をご覧ください。
- Data Lake Analytics の概要については、[Azure Data Lake Analytics の使用開始に関するチュートリアル](data-lake-analytics-get-started-portal.md)のページを参照してください。
- Data Lake Tools for Visual Studio の使用方法については、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。
- アセンブリの開発に関する情報については、「[Develop U-SQL assemblies for Azure Data Lake Analytics jobs (Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発)](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。
