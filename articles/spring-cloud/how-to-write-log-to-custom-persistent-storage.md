---
title: Logback を使用して Azure Spring Cloud 内のカスタム永続ストレージにログを書き込む方法 | Microsoft Docs
description: Logback を使用して Azure Spring Cloud 内のカスタム永続ストレージにログを書き込む方法。
author: karlerickson
ms.author: xuycao
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: 558ca29f8faa03857d4debe760c4859ab43b17e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404304"
---
# <a name="how-to-use-logback-to-write-logs-to-custom-persistent-storage"></a>Logback を使用してカスタム永続ストレージにログを書き込む方法

**この記事の適用対象:** ✔️ Java

この記事では、Logback を読み込んで、Azure Spring Cloud 内のカスタム永続ストレージにログを書き込む方法について説明します。

> [!NOTE]
> 次の名前のいずれかを持つファイルがアプリケーションのクラスパス内にある場合、それは Spring Boot によって Logback の既定の構成に自動的に読み込まれます。
> - *logback-spring.xml*
> - *logback.xml*
> - *logback-spring.groovy*
> - *logback.groovy*

## <a name="prerequisites"></a>前提条件

* 既存のストレージ リソースが Azure Spring Cloud インスタンスにバインドされている。 ストレージ リソースをバインドする必要がある場合は、「[Azure Spring Cloud で独自の永続ストレージを有効にする方法](./how-to-custom-persistent-storage.md)」を参照してください。
* ご利用のアプリケーションに Logback 依存関係が含まれている。 Logback の詳細については、「[A Guide To Logback](https://www.baeldung.com/logback)」 (Logback のガイド) を参照してください。
* Azure CLI 用の [Azure Spring Cloud 拡張機能](/cli/azure/azure-cli-extensions-overview)

## <a name="edit-the-logback-configuration-to-write-logs-into-a-specific-path"></a>ログを特定のパスに書き込むように Logback 構成を編集する

logback-spring.xml サンプル ファイルを使用すれば、ログの書き込み先とするパスを設定できます。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="Console"
              class="ch.qos.logback.core.ConsoleAppender">
        <!-- please feel free to customize the log layout -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 'LOGS' here is a value to be read from the application's environment variable -->
        <file>${LOGS}/spring-boot-logger.log</file>
        <!-- please feel free to customize the log layout pattern -->
        <encoder
                class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!-- LOG everything at the INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <!-- LOG "com.baeldung*" at the TRACE level -->
    <logger name="com.baeldung" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>

</configuration>
```

前の例では、アプリケーションのログの書き込み先とするパスに `{LOGS}` という名前のプレースホルダーが 2 つあります。 コンソールとご利用の永続ストレージの両方にログが書き込まれるようにするには、環境変数 `LOGS` に値を割り当てる必要があります。 

## <a name="use-the-azure-cli-to-create-and-deploy-logback-to-write-logs-to-persistent-storage"></a>Azure CLI を使用して永続的なストレージにログを書き込むための Logback を作成してデプロイする

1. 次のコマンドを使用すれば、永続的なストレージを有効にし、環境変数を設定して、Azure Spring Cloud 内にアプリケーションを作成することができます。

   ```azurecli
   az spring-cloud app create \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-json-file> \
        --env LOGS=/byos/logs
   ```

    永続的なストレージは、ご利用のアプリケーションで使用されているものと同じパスにマウントする必要があります。 create コマンド内の `--persistent-storage` パラメーターに渡される JSON ファイルの例を次に示します。 上記の CLI コマンド内の環境変数と、下記の `mountPath` プロパティには、必ず同じ値を渡すようにしてください。 

    ```json
    {
        "customPersistentDisks": [
            {
                "storageName": "<Storage-Resource-Name>",
                "customPersistentDiskProperties": {
                    "type": "AzureFileVolume",
                    "shareName": "<Azure-File-Share-Name>",
                    "mountPath": "/byos/logs",
                    "readOnly": false
                }
            }
        ]
    }
    ```
  
1. 次のコマンドを使用して、ご利用のアプリケーションをデプロイします。

   ```azurecli
   az spring-cloud app deploy \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --jar-path <path-to-jar-file>
   ```

1. 次のコマンドを使用して、そのアプリケーションのコンソール ログを確認します。

   ```azurecli
   az spring-cloud app logs \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name>
   ```

    バインドした Azure Storage アカウント リソースにアクセスし、永続ストレージとしてアタッチされた Azure ファイル共有を見つけます。 この例では、ご利用の Azure ファイル共有のホーム パスにある *spring-boot-logger.log* ファイルにログが書き込まれます。 ローテーションされたログ ファイルはすべて、その Azure ファイル共有内の */archived* フォルダーに格納されます。

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud の構造化アプリケーション ログ](./structured-app-log.md)
* [診断設定を使用したログとメトリックの分析](./diagnostic-services.md)