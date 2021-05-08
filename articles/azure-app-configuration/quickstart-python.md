---
title: Python アプリで Azure App Configuration を使用するためのクイックスタート | Microsoft Docs
description: このクイックスタートでは、Azure App Configuration を使用して Python アプリを作成します。コードとは別に、アプリケーション設定のストレージと管理を一元化します。
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 1be74ceeb1f4808a8df8f56b71b66aa870cdd91d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800875"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>クイックスタート: Azure App Configuration を使用した Python アプリの作成

このクイックスタートでは、[Python 用の Azure App Configuration クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)を使用し、Azure App Configuration でアプリケーション設定のストレージと管理を一元化します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- Python 2.7 または 3.6 以降 - Windows での Python の設定について詳しくは、[Windows での Python に関するドキュメント]( https://docs.microsoft.com/windows/python/)を参照してください

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

8. **[適用]** を選択します。

## <a name="setting-up-the-python-app"></a>Python アプリの設定

1. このチュートリアルでは、プロジェクト用に *app-configuration-quickstart* という名前の新しいディレクトリを作成します。

    ```console
    mkdir app-configuration-quickstart
    ```

1. 新しく作成した *app-configuration-quickstart* ディレクトリに切り替えます。

    ```console
    cd app-configuration-quickstart
    ```

1. `pip install` コマンドを使用して、Azure App Configuration クライアント ライブラリをインストールします。

    ```console
    pip install azure-appconfiguration
    ```

1. *app-configuration-quickstart* ディレクトリに *app-configuration-quickstart.py* という名前の新しいファイルを作成し、次のコードを追加します。

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> このクイックスタートのコード スニペットは、Python 用の App Configuration クライアント ライブラリで作業を開始する際に役立ちます。 ご自分のアプリケーションでは、必要に応じて例外の処理についても検討してください。 例外の処理の詳細については、[Python SDK のドキュメント](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)を参照してください。

## <a name="configure-your-app-configuration-connection-string"></a>App Configuration 接続文字列の構成

1. **AZURE_APP_CONFIG_CONNECTION_STRING** という名前の環境変数を設定し、それを自分の App Configuration ストアへのアクセス キーに設定します。 コマンド ラインで次のコマンドを実行します。

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. 変更を有効にするために、コマンド プロンプトを再起動します。 環境変数の値を出力して、正しく設定されていることを確認します。

## <a name="code-samples"></a>コード サンプル

このセクションのサンプル コード スニペットでは、Python 用の App Configuration クライアント ライブラリを使用して一般的な操作を実行する方法を示します。 前に作成した *app-configuration-quickstart.py* ファイルの `try` ブロックにこれらのコード スニペットを追加します。

> [!NOTE]
> App Configuration クライアント ライブラリは、キー値オブジェクトを `ConfigurationSetting` として参照します。 そのため、この記事では、App Configuration ストア内の **キー値** が **構成設定** として参照されます。

* [App Configuration ストアに接続する](#connect-to-an-app-configuration-store)
* [構成設定を取得する](#get-a-configuration-setting)
* [構成設定を追加する](#add-a-configuration-setting)
* [構成設定のリストを取得する](#get-a-list-of-configuration-settings)
* [構成設定をロックする](#lock-a-configuration-setting)
* [構成設定をロック解除する](#unlock-a-configuration-setting)
* [構成設定を更新する](#update-a-configuration-setting)
* [構成設定を削除する](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

次のコード スニペットでは、ご自分の環境変数に格納された接続文字列を使用して、**AzureAppConfigurationClient** のインスタンスを作成します。

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>構成設定を取得する

次のコード スニペットでは、`key` 名を指定して構成設定を取得します。

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>構成設定を追加する

次のコード スニペットでは、`key` および `value` フィールドを備えた `ConfigurationSetting` オブジェクトを作成し、`add_configuration_setting` メソッドを呼び出します。 このメソッドでは、自分のストアに既に存在する構成設定を追加しようとすると例外がスローされます。 この例外を回避したい場合は、代わりに [set_configuration_setting](#update-a-configuration-setting) メソッドを使用できます。

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>構成設定のリストを取得する

次のコード スニペットでは、構成設定のリストを取得します。 `key_filter` および `label_filter` 引数を指定して、それぞれ `key` と `label` に基づいてキー値をフィルターできます。 フィルターの詳細については、[構成設定を照会](./concept-key-value.md#query-key-values)する方法を確認してください。

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>構成設定をロックする

App Configuration 内のキー値のロック状態は、`ConfigurationSetting` オブジェクトの `read_only` 属性によって示されます。 `read_only` が `True` の場合、設定はロックされています。 `read_only=True` 引数を使用して `set_read_only` メソッドを呼び出し、構成設定をロックできます。

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>構成設定をロック解除する

`ConfigurationSetting` の `read_only` 属性が `False` の場合、設定はロック解除されています。 `read_only=False` 引数を使用して `set_read_only` メソッドを呼び出し、構成設定をロック解除できます。

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>構成設定を更新する

`set_configuration_setting` メソッドを使用して、既存の設定を更新したり新しい設定を作成したりできます。 次のコード スニペットでは、既存の構成設定の値を変更します。

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>構成設定を削除する

次のコード スニペットでは、`key` 名を指定して構成設定を削除します。

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>アプリを実行する

このクイックスタートで作成した Python アプリでは、Azure App Configuration クライアント ライブラリを使用して、Azure portal を通じて作成した構成設定の取得、新しい設定の追加、既存の設定が記載されたリストの取得、設定のロックとロック解除、設定の更新、最後に設定の削除を行います。

この時点で、*app-configuration-quickstart.py* ファイルに次のコードが含まれている必要があります。

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

コンソール ウィンドウで、*app-configuration-quickstart.py* ファイルが格納されているディレクトリに移動し、次の Python コマンドを実行してアプリを実行します。

```console
python app-configuration-quickstart.py
```

次の出力が表示されます。

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、Python アプリのキー値を操作する方法について学習しました。

その他のコード サンプルについては、次を参照してください。

> [!div class="nextstepaction"]
> [Azure App Configuration クライアント ライブラリのサンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
