---
title: 'クイックスタート: Python を使用して Purview アカウントを作成する'
description: Python を使用して Azure Purview アカウントを作成します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.openlocfilehash: 7a6d13da2ee3138e6dfc5eca4a5b75f454f90457
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212488"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>クイックスタート: Python を使用して Purview アカウントを作成する

このクイックスタートでは、Python を使用してプログラムで Purview アカウントを作成します。 [Purview に関する Python リファレンス](/python/api/azure-mgmt-purview/)を利用できますが、この記事では、Python を使用してアカウントを作成するために必要なすべての手順について説明します。

Azure Purview は、データ環境の管理と制御に役立つデータ ガバナンス サービスです。 Purview では、オンプレミス、マルチクラウド、サービスとしてのソフトウェア (SaaS) のソース全体のデータに接続することで、情報の最新のマップが作成されます。 これにより、機密データが識別されて分類され、エンドツーエンドの系列が提供されます。 データ コンシューマーは組織全体のデータを検出でき、データ管理者はデータを監査し、セキュリティで保護し、データの適切な使用を確保することができます。

Purview の詳細については、[概要ページを参照してください](overview.md)。 組織全体に Purview をデプロイする方法の詳細については、[デプロイのベスト プラクティスを参照してください](deployment-best-practices.md)。

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

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
    > "azure-identity" パッケージは、いくつかの共通の依存関係に関して、"azure-cli" と競合する可能性があります。 認証の問題が発生した場合は、"azure-cli" とその依存関係を削除するか、"azure-cli" パッケージがインストールされていないクリーン マシンを使用してください。

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

1. **Purview アカウント** を作成する次のコードを **Main** メソッドに追加します。 リソース グループが既に存在する場合は、最初の `create_or_update` ステートメントをコメント アウトします。

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

2. プログラムの実行時に **main** メソッドを呼び出す次のステートメントを追加します。

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

アプリケーションをビルドして起動します。 コンソールにより、Purview アカウントの作成の進行状況が出力されます。 完了するまで待機してください。
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

Purview アカウントを削除するには、プログラムに次のコードを追加して実行します。

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>次のステップ

このチュートリアルのコードでは、Purview アカウントを作成し、Purview アカウントを削除します。 これで、Python SDK をダウンロードし、Purview アカウントに対して実行できるその他のリソース プロバイダー アクションについて学習することができます。

次の記事に従い、Purview Studio を操作する方法、コレクションを作成する方法、Purview にアクセス権を付与する方法について学習してください。

* [Purview Studio の使用方法](use-purview-studio.md)
* [コレクションの作成](quickstart-create-collection.md)
* [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)