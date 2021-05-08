---
title: 'クイックスタート: Python を使用して Purview アカウントを作成する'
description: Python を使用して Azure Purview アカウントを作成します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387150"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>クイックスタート: Python を使用して Purview アカウントを作成する

このクイックスタートでは、Python を使用して Purview アカウントを作成します。 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* ご使用のアカウントには、サブスクリプションにリソースを作成するためのアクセス許可が必要です。

* **ストレージ アカウント** および **EventHub 名前空間** の作成をすべてのアプリケーションに禁止する **Azure Policy** がある場合は、タグを使用してポリシーの例外を作成する必要があります。これは、Purview アカウントを作成する過程で入力できます。 その主な理由は、作成した各 Purview アカウントでマネージド リソース グループを作成し、また、そのリソース グループ内にストレージ アカウントと EventHub 名前空間を作成する必要があるためです。 詳細については、[Catalog ポータルの作成](create-catalog-portal.md)に関する記事をご覧ください


## <a name="install-the-python-package"></a>Python パッケージをインストールする

1. 管理者特権でターミナルまたはコマンド プロンプトを開きます。 
2. まず、Azure 管理リソースの Python パッケージをインストールします。

    ```python
    pip install azure-mgmt-resource
    ```
3. Purview 用の Python パッケージをインストールするには、次のコマンドを実行します。

    ```python
    pip install azure-mgmt-purview
    ```

    [Purview 用の Python SDK](https://github.com/Azure/azure-sdk-for-python) では、Python 2.7、3.3、3.4、3.5、3.6、および 3.7 がサポートされています。

4. Azure Identity Authentication 用の Python パッケージをインストールするには、次のコマンドを実行します。

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > "azure-identity" パッケージは、いくつかの共通の依存関係に関して、"azure-cli" と競合する可能性があります。 認証の問題が発生した場合は、"azure-cli" とその依存関係を削除するか、"azure-cli" パッケージがインストールされていないクリーン マシンを使用して解決してください。
    
## <a name="create-a-purview-client"></a>Purview クライアントを作成する

1. **purview.py** という名前のファイルを作成します。 次のステートメントを追加して、名前空間への参照を追加します。

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. PurviewManagementClient クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。 このオブジェクトを使用して、Purview アカウントの作成と削除、名前の可用性やその他のリソース プロバイダー操作の確認を行います。
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Purview アカウントを作成する

**Purview アカウント** を作成する次のコードを **Main** メソッドに追加します。 リソース グループが既に存在する場合は、最初の `create_or_update` ステートメントをコメント アウトします。

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

プログラムの実行時に **main** メソッドを呼び出す次のステートメントを追加します。

```python
# Start the main method
main()
```

## <a name="full-script"></a>完全なスクリプト

完全な Python コードを次に示します。

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>コードの実行

アプリケーションをビルドして起動し、パイプラインの実行を確認します。

コンソールに、データ ファクトリ、リンクされたサービス、データセット、パイプライン、およびパイプラインの実行の作成の進捗状況が表示されます。 コピー アクティビティの実行の詳細と、データの読み取り/書き込みのサイズが表示されるまで待ちます。 次に、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、変数で指定したように BLOB が "inputBlobPath" から "outputBlobPath" にコピーされていることを確認します。

出力例を次に示します。

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>出力を検証する

Azure portal の **[Purview アカウント]** ページに移動し、上記のコードを使用して作成されたアカウントを確認します。 

## <a name="delete-purview-account"></a>Purview アカウントを削除する

Purview アカウントを削除するには、プログラムに次のコードを追加します。

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>次のステップ

このチュートリアルのコードでは、Purview アカウントを作成し、Purview アカウントを削除します。 これで、Python SDK をダウンロードし、Purview アカウントに対して実行できるその他のリソース プロバイダー アクションについて学習することができます。

次の記事に進んで、Azure Purview アカウントへのアクセスをユーザーに許可する方法を学習してください。 

> [!div class="nextstepaction"]
> [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)
