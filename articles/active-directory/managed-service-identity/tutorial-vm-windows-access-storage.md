---
title: Windows VM マネージド ID を使用して Azure Storage にアクセスする
description: Windows VM マネージド ID を使用して Azure Storage にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: e001907b9df77eff1455043a3fd7ce5533838fcc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056176"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>チュートリアル: Windows VM マネージド ID を使用して Azure Storage にアクセスする

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシンのマネージド ID を有効にし、その ID を使用して Azure Storage にアクセスする方法について説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * 新しいリソース グループに Windows 仮想マシンを作成する 
> * Windows 仮想マシン (VM) でマネージド ID を有効にする
> * ストレージ アカウントに BLOB コンテナーを作成する
> * Windows VM のマネージド ID にストレージ アカウントへのアクセスを許可する 
> * アクセス権を取得し、それを使用して Azure Storage を呼び出す 

> [!NOTE]
> Azure Storage の Azure Active Directory 認証は、パブリック プレビューの段階です。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>新しいリソース グループに Windows 仮想マシンを作成する

このセクションでは、マネージド ID が後で付与される Windows VM を作成します。

1.  Azure Portal の左上隅にある **[+/新しいサービスの作成]** ボタンをクリックします。
2.  **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。 
3.  仮想マシンの情報を入力します。 ここで作成した**ユーザー名**と**パスワード**は、仮想マシンへのログインに使用する資格情報になります。
4.  ドロップダウンで仮想マシンの適切な**サブスクリプション**を選択します。
5.  仮想マシンを作成する新しい**リソース グループ**を選択するには、**[新規作成]** を選択します。 完了したら、**[OK]** をクリックします。
6.  VM のサイズを選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 設定ブレードで、既定値のまま **[OK]** をクリックします。

    ![イメージ テキスト](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>VM のマネージド ID を有効にする

仮想マシンのマネージド ID を使用すると、コードに資格情報を挿入しなくても、Azure AD からアクセス トークンを取得できます。 Azure Portal で仮想マシンのマネージド ID を有効にすると、内部では VM が Azure AD に登録されてマネージド ID が作成され、VM で ID が構成されます。 

1. 新しい仮想マシンのリソース グループに移動し、前の手順で作成した仮想マシンを選択します。
2. **[設定]** カテゴリで、**[構成]** をクリックします。
3. マネージド ID を有効にするには、**[はい]** を選択します。
4. **[保存]** をクリックして構成を適用します。 

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

このセクションでは、ストレージ アカウントを作成します。 

1. Azure Portal の左上隅にある **[+ リソースの作成]** ボタンをクリックします。
2. **[ストレージ]**、**[ストレージ アカウント - Blob、File、Table、Queue]** の順にクリックします。
3. **[名前]** で、ストレージ アカウントの名前を入力します。  
4. **[デプロイ モデル]** と **[アカウントの種類]** がそれぞれ **[Resource manager]** と **[ストレージ (汎用 v1)]** に設定されている必要があります。 
5. **[サブスクリプション]** と **[リソース グループ]** が、前の手順で VM を作成したときに指定したものと一致していることを確認します。
6. **Create** をクリックしてください。

    ![新しいストレージ アカウントを作成する](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>BLOB コンテナーを作成し、ファイルをストレージ アカウントにアップロードする

ファイルには Blob Storage が必要であるため、ファイルを格納する BLOB コンテナーを作成する必要があります。 次に、新しいストレージ アカウントで、BLOB コンテナーにファイルをアップロードします。

1. 新たに作成したストレージ アカウントに戻ります。
2. **[Blob service]** の **[コンテナー]** をクリックします。
3. ページの上部にある **[+ コンテナー]** をクリックします。
4. **[新しいコンテナー]** で、コンテナーの名前を入力し、**[パブリック アクセス レベル]** で既定値を保持します。

    ![ストレージ コンテナーの作成](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 任意のエディターを使用して、ローカル コンピューターに *hello world.txt* という名前のファイルを作成します。  ファイルを開き、"Hello world! :)" というテキストを (引用符なしで) 追加し、保存します。 
6. 新しく作成したコンテナーにファイルをアップロードします。コンテナー名をクリックしてから **[アップロード]** をクリックします。
7. **[BLOB のアップロード]** ウィンドウの **[ファイル]** で、フォルダー アイコンをクリックし、ローカル コンピューターの **hello_world.txt** を参照してファイルを選択して、**[アップロード]** をクリックします。
    ![テキスト ファイルをアップロードする](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>VM に Azure Storage コンテナーへのアクセスを許可する 

VM のマネージド ID を使用して、Azure Storage Blob のデータを取得できます。   

1. 新たに作成したストレージ アカウントに戻ります。  
2. 左側のパネルの **[アクセス制御 (IAM)]** リンクをクリックします。  
3. ページの上部にある **[+ 追加]** をクリックして、VM 用に新しいロールの割り当てを追加します。
4. **[ロール]** で、ドロップダウンから **[ストレージ BLOB データ閲覧者 (プレビュー)]** を選択します。 
5. 次のドロップダウンで、**[アクセスの割り当て先]** として **[仮想マシン]** を選択します。  
6. 次に、適切なサブスクリプションが **[サブスクリプション]** ドロップダウンにリストされていることを確認してから、**[リソース グループ]** を **[すべてのリソース グループ]** に設定します。  
7. **[選択]** で、VM を選択し、**[保存]** をクリックします。 

    ![アクセス許可の割り当て](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>アクセス トークン取得し、それを使用して Azure Storage を呼び出す 

Azure Storage は Azure AD 認証をネイティブにサポートするため、マネージド ID を使用して取得したアクセス トークンを直接受け入れることができます。 これは Azure Storage の Azure AD との統合の一部であり、接続文字列に資格情報を提供することとは異なります。

次に示すのは、アクセス トークンを使用して Azure Storage への接続を開き、以前に作成したファイルの内容を読み取る .NET コードの例です。 このコードは、VM のマネージド ID にアクセスできる VM で実行する必要があります。 アクセス トークン メソッドを使用するには、.Net Framework 4.6 以降が必要です。 適宜、`<URI to blob file>` の値を置き換えます。 この値は、以前に作成して Blob Storage にアップロードしたファイルに移動し、**[概要]** ページの **[プロパティ]** で **URL** をコピーすることで取得できます。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

応答には、次のようなファイルの内容が含まれています。

`Hello world! :)`

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Windows 仮想マシンのマネージド ID を使用して Azure Storage にアクセスする方法を説明しました。  Azure Storage の詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



