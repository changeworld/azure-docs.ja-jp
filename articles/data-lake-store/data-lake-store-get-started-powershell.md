---
title: "Data Lake Store の使用 | Microsoft Docs"
description: "Azure PowerShell を使用して、Data Lake Store アカウントを作成し、基本的な操作を実行する"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c70eff85416b6b41eb455edc430a66b588dec41


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Azure PowerShell で Azure Data Lake Analytics の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> 
> 

Azure PowerShell を使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Store の詳細については、[Data Lake Store の概要](data-lake-store-overview.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## <a name="authentication"></a>認証
この記事では、Data Lake Store に対する認証に、Azure アカウントの資格情報を入力する比較的単純な方法を使用しています。 その後、Data Lake Store アカウントとファイル システムに対するアクセス レベルは、そのログイン ユーザーのアクセス レベルで管理されます。 ただし、Data Lake Store には他の認証方法も存在します (**エンド ユーザー認証**と**サービス間認証**)。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する
1. デスクトップで、新しい Windows PowerShell ウィンドウを開き、次のスニペットを入力して Azure アカウントにログインし、サブスクリプションを設定して、Data Lake Store プロバイダーを登録します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。
   
        # Log in to your Azure account
        Login-AzureRmAccount
   
        # List all the subscriptions associated to your account
        Get-AzureRmSubscription
   
        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>
   
        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Azure Data Lake Store アカウントは、Azure リソース グループに関連付けられます。 まず、Azure リソース グループを作成します。
   
        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"
   
    ![Azure リソース グループを作成する](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. Azure Data Lake Store アカウントを作成します。 指定する名前には、小文字と数字のみを含める必要があります。
   
        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"
   
    ![Azure Data Lake Store アカウントを作成する](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")
4. アカウントが正常に作成されたことを確認します。
   
        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName
   
    この出力は **True**になります。

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Azure Data Lake Store でディレクトリ構造を作成する
Azure Data Lake Store アカウントにディレクトリを作成し、データを管理したり格納したりすることができます。

1. ルート ディレクトリを指定します。
   
        $myrootdir = "/"
2. 指定したルートの下に **mynewdirectory** という新しいディレクトリを作成します。
   
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 新しいディレクトリが正常に作成されたことを確認します。
   
        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir
   
    出力は次のように表示されます。
   
    ![ディレクトリを確認する](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Azure Data Lake Store にデータをアップロードする
データは、ルート レベルで直接 Data Lake Store に、またはアカウント内で作成したディレクトリにアップロードすることができます。 以下のスニペットは、前のセクションで作成したディレクトリ (**mynewdirectory**) にいくつかのサンプル データをアップロードする方法を示します。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。 ファイルをダウンロードし、コンピューター上のローカル ディレクトリ (C:\sampledata\. など) に保存します。

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Data Lake Store からデータの名前変更、ダウンロード、および削除を行う
ファイルの名前を変更するには、次のコマンドを使用します。

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

ファイルをダウンロードするには、次のコマンドを使用します。

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

ファイルを削除するには、次のコマンドを使用します。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、項目を削除します。 削除する複数のファイルがある場合は、すべてのパスをコンマで区切って指定できます。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを削除する
Data Lake Store アカウントを削除するには、以下のコマンドを使用します。

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、アカウントを削除します。

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO2-->


