---
title: 依存関係とサードパーティ製ライブラリを Azure Functions に取り込む
description: ファイルまたはサード パーティ製ライブラリを使用する方法について説明します
ms.date: 4/6/2020
ms.topic: article
zone_pivot_groups: bring-third-party-dependency-programming-functions
ms.openlocfilehash: be01f07a9906da58d080060185104aef29f78b77
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265307"
---
# <a name="bring-dependencies-or-third-party-library-to-azure-functions"></a>依存関係またはサードパーティ製ライブラリを Azure Functions に取り込む

この記事では、サードパーティの依存関係を関数アプリに取り込む方法について説明します。 サードパーティの依存関係の例としては、JSON ファイル、バイナリ ファイル、機械学習モデルがあります。 

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * Functions Code プロジェクトを使用して依存関係を取り込む 
::: zone pivot="programming-language-python"
> [!div class="checklist"]
> * Azure Fileshare をマウントして依存関係を取り込む
::: zone-end

## <a name="bring-in-dependencies-from-the-project-directory"></a>プロジェクト ディレクトリから依存関係を取り込む
::: zone pivot="programming-language-python"
依存関係を取り込む最も簡単な方法の 1 つは、ファイル/成果物を Functions プロジェクトディレクトリ構造の関数アプリ コードと一緒に置く方法です。 Python 関数プロジェクトのディレクトリ サンプルの例を次に示します。
```
<project_root>/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - dependencies/
 | | - dependency1
 | - .funcignore
 | - host.json
 | - local.settings.json
```
Functions アプリ プロジェクト ディレクトリ内のフォルダーに依存関係を配置すると、dependencies フォルダーがコードと共にデプロイされます。 その結果、関数コードは、ファイル システム API を介してクラウド内の依存関係にアクセスできます。 

### <a name="accessing-the-dependencies-in-your-code"></a>コード内の依存関係へのアクセス

```<project_root>/ffmpeg_lib``` ディレクトリに置く ```ffmpeg``` 依存関係にアクセスして実行する例を次に示 します。 


```python
import logging

import azure.functions as func
import subprocess

FFMPEG_RELATIVE_PATH = "../ffmpeg_lib/ffmpeg"

def main(req: func.HttpRequest,
         context: func.Context) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    # context.function_directory returns the current directory in which functions is executed 
    ffmpeg_path = "/".join([str(context.function_directory), FFMPEG_RELATIVE_PATH])
    
    try:
        byte_output  = subprocess.check_output([ffmpeg_path, command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```
>[!NOTE]
> Linux 環境で ffmpeg バイナリに `Execute` 権限を提供するために `chmod` を使用する必要がある場合があります
::: zone-end

::: zone pivot="programming-language-java"
依存関係を取り込む最も簡単な方法の 1 つは、ファイル/成果物を Functions プロジェクトディレクトリ構造の関数アプリ コードと一緒に置く方法です。 Java 関数プロジェクトのディレクトリ サンプルの例を次に示します。
```
<project_root>/
 | - src/
 | | - main/java/com/function
 | | | - Function.java
 | | - test/java/com/function
 | - artifacts/
 | | - dependency1
 | - host.json
 | - local.settings.json
 | - pom.xml
```
具体的には、java の場合は、リソースをコピーするときに、ビルド/ターゲット フォルダーに成果物を具体的に含める必要があります。 Maven でこれを行う方法の例を次に示します。

```xml
...
<execution>
    <id>copy-resources</id>
    <phase>package</phase>
    <goals>
        <goal>copy-resources</goal>
    </goals>
    <configuration>
        <overwrite>true</overwrite>
        <outputDirectory>${stagingDirectory}</outputDirectory>
        <resources>
            <resource>
                <directory>${project.basedir}</directory>
                <includes>
                    <include>host.json</include>
                    <include>local.settings.json</include>
                    <include>artifacts/**</include>
                </includes>
            </resource>
        </resources>
    </configuration>
</execution>
...
```
Functions アプリ プロジェクト ディレクトリ内のフォルダーに依存関係を配置すると、dependencies フォルダーがコードと共にデプロイされます。 その結果、関数コードは、ファイル システム API を介してクラウド内の依存関係にアクセスできます。 

### <a name="accessing-the-dependencies-in-your-code"></a>コード内の依存関係へのアクセス

```<project_root>/ffmpeg_lib``` ディレクトリに置く ```ffmpeg``` 依存関係にアクセスして実行する例を次に示 します。 


```java
public class Function {
    final static String BASE_PATH = "BASE_PATH";
    final static String FFMPEG_PATH = "/artifacts/ffmpeg/ffmpeg.exe";
    final static String HELP_FLAG = "-h";
    final static String COMMAND_QUERY = "command";

    @FunctionName("HttpExample")
    public HttpResponseMessage run(
            @HttpTrigger(
                name = "req",
                methods = {HttpMethod.GET, HttpMethod.POST},
                authLevel = AuthorizationLevel.ANONYMOUS)
                HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) throws IOException{
        context.getLogger().info("Java HTTP trigger processed a request.");
            
        // Parse query parameter
        String flags = request.getQueryParameters().get(COMMAND_QUERY);
        
        if (flags == null || flags.isBlank()) {
            flags = HELP_FLAG;
        }

        Runtime rt = Runtime.getRuntime();
        String[] commands = { System.getenv(BASE_PATH) + FFMPEG_PATH, flags};
        Process proc = rt.exec(commands);
        
        BufferedReader stdInput = new BufferedReader(new 
        InputStreamReader(proc.getInputStream()));
   
        String out = stdInput.lines().collect(Collectors.joining("\n"));
        if(out.isEmpty()) {
            BufferedReader stdError = new BufferedReader(new 
                InputStreamReader(proc.getErrorStream()));
            out = stdError.lines().collect(Collectors.joining("\n"));
        }
        return request.createResponseBuilder(HttpStatus.OK).body(out).build();

    }
```
>[!NOTE]
> このコード スニペットを Azure で動作させるには、「/home/site/wwwroot」の値を使用して 「BASE_PATH」のカスタムアプリケーション設定を指定する必要があります。
::: zone-end


::: zone pivot="programming-language-python"
## <a name="bring-dependencies-by-mounting-a-file-share"></a>ファイル共有をマウントして依存関係を取り込む

Linux で関数アプリを実行する場合は、サードパーティの依存関係を取り込む別の方法があります。 Functions を使用すると、Azure Files でホストされているファイル共有をマウントできます。 アプリケーション コードから依存関係または成果物を分離する場合は、この方法を検討してください。

まず、Azure ストレージ アカウントを作成する必要があります。 アカウントでは、Azure files でファイル共有を作成する必要もあります。 これらのリソースを作成するには、こちらの[ガイド](../storage/files/storage-how-to-use-files-portal.md)に従ってください

ストレージアカウントとファイル共有を作成した後、次の例に示すように、 [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) コマンドを使用して、ファイル共有を関数アプリにアタッチします。

```console
az webapp config storage-account add \
  --name < Function-App-Name > \
  --resource-group < Resource-Group > \
  --subscription < Subscription-Id > \
  --custom-id < Unique-Custom-Id > \
  --storage-type AzureFiles \
  --account-name < Storage-Account-Name > \
  --share-name < File-Share-Name >  \
  --access-key < Storage-Account-AccessKey > \
  --mount-path </path/to/mount>
```



|       フラグ    |  値     |
| ------------- | ---------------------------------- | 
| custom-id      |  任意の一意の文字列 |
| storage-type      |  現在サポートされているのは AzureFiles のみです |
| share-name      |  Pre-existing share |
| mount-path     |   コンテナー内で共有にアクセスできるパス。 値は `/dir-name` 形式である必要があり、`/home` で始めることはできません |

ファイル共有の構成を変更または削除するその他のコマンドについては、[こちら](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-update)を参照してください


### <a name="uploading-the-dependencies-to-azure-files"></a>依存関係を Azure Files にアップロードする

依存関係を Azure Files にアップロードするには、Azure portal を使用することもオプションの 1 つです。 ポータルを使用して依存関係をアップロードする手順については、この [ ガイド ](../storage/files/storage-how-to-use-files-portal.md#upload-a-file) を参照してください。 依存関係を Azure Files にアップロードするその他のオプションには、[Azure CLI](../storage/files/storage-how-to-use-files-portal.md#upload-a-file) と [PowerShell](../storage/files/storage-how-to-use-files-portal.md#upload-a-file) の使用もあります。


### <a name="accessing-the-dependencies-in-your-code"></a>コード内の依存関係へのアクセス

ファイル共有に依存関係がアップロードされると、コードから依存関係にアクセスできます。 マウントされた共有は、```/path/to/mount``` など指定された *mount-path* で使用できます。 ファイル システム API を使用してターゲット ディレクトリにアクセスできます。

次の例は、マウントされたファイル共有に格納されている `ffmpeg` ライブラリにアクセスする HTTP トリガー コードを示しています。

```python
import logging

import azure.functions as func
import subprocess 

FILE_SHARE_MOUNT_PATH = os.environ['FILE_SHARE_MOUNT_PATH']
FFMPEG = "ffmpeg"

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    try:
        byte_output  = subprocess.check_output(["/".join(FILE_SHARE_MOUNT_PATH, FFMPEG), command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```

このコードを Azure の関数アプリにデプロイする場合は、`FILE_SHARE_MOUNT_PATH` のキー名とマウントされたファイル共有パスの値 (この例では `/azure-files-share`) を使用して[アプリ設定を作成する](functions-how-to-use-azure-function-app-settings.md#settings)必要 があります。 ローカル デバッグを行うには、依存関係がローカル コンピューターに格納されているファイル パス に `FILE_SHARE_MOUNT_PATH` を設定する必要があります。 `local.settings.json` を使用して `FILE_SHARE_MOUNT_PATH` を設定する例を次に示します。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "FILE_SHARE_MOUNT_PATH" : "PATH_TO_LOCAL_FFMPEG_DIR"
  }
}

```
::: zone-end



## <a name="next-steps"></a>次のステップ

+ [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)
+ [Azure Functions の Java 開発者向けガイド](functions-reference-java.md)
+ [Azure Functions 開発者向けリファレンス](functions-reference.md)