---
title: Apache Kafka SSL 暗号化および認証 - Azure HDInsight
description: Kafka ブローカー間や、Kafka クライアントと Kafka ブローカー間の通信用に SSL 暗号化を設定します。 クライアントの SSL 認証を設定します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 180b7c203755553c343e0f7fc65c93092b330124
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751311"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight の Apache Kafka 用に Secure Sockets Layer (SSL) 暗号化および認証を設定する

この記事では、Apache Kafka クライアントと Apache Kafka ブローカー間の通信に SSL 暗号化を設定する方法を示します。 クライアントの認証を設定する方法についても説明します (双方向 SSL とも呼ばれます)。

> [!Important]
> Kafka アプリケーションでは、Java クライアントとコンソール クライアントの 2 種類のクライアントを使用できます。 生成と使用の両方に SSL を使用できるのは、Java クライアント `ProducerConsumer.java` のみです。 コンソール プロデューサー クライアント `console-producer.sh` は、SSL では機能しません。

## <a name="apache-kafka-broker-setup"></a>Apache Kafka ブローカーのセットアップ

Kafka SSL ブローカーのセットアップでは、4 つの HDInsight クラスター VM が次のように使用されます。

* ヘッドノード 0 - 証明機関 (CA)
* ワーカー ノード 0、1、2 - ブローカー

> [!Note] 
> このガイドでは自己署名証明書を使用しますが、最も安全なソリューションは、信頼された CA によって発行された証明書を使うことです。

ブローカーのセットアップ プロセスの概要は次のとおりです。

1. 次の手順を 3 つのワーカー ノードのそれぞれで繰り返します。

    1. 証明書を作成します。
    1. 証明書署名要求を作成します。
    1. 証明書署名要求を証明機関 (CA) に送信します。
    1. CA にサインインし、要求に署名します。
    1. 署名済みの証明書をワーカー ノードに SCP で送り返します。
    1. CA の公開証明書をワーカー ノードに SCP で送信します。

1. すべての証明書が揃ったら、証明書を証明書ストアに格納します。
1. Ambari に移動し、構成を変更します。

次の詳細な手順を使って、ブローカーのセットアップを完了します。

> [!Important]
> 次のコード スニペットで、wnX は 3 つのワーカー ノードの 1 つの省略形であり、`wn0`、`wn1`、または `wn2` のいずれか適切なものに置き換える必要があります。 `WorkerNode0_Name` と `HeadNode0_Name` はそれぞれのコンピューターの名前に置き換える必要があります。

1. ヘッド ノード 0 で初期セットアップを実行します。HDInsight の場合は、証明機関 (CA) のロールを設定します。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 各ブローカー (ワーカー ノード 0、1、2) で同じ初期セットアップを実行します。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 各ワーカー ノードで、次のコード スニペットを使って以下の手順を実行します。
    1. キーストアを作成し、新しいプライベート証明書を設定します。
    1. 証明書署名要求を作成します。
    1. 証明書署名要求を CA (headnode0) に SCP で送信します

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. CA コンピューター上で、次のコマンドを実行して CA 証明書と CA キー ファイルを作成します。

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. CA のコンピューターに移動し、受け取ったすべての証明書署名要求に署名します。

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. CA (headnode0) からワーカー ノードに、署名済みの証明書を返送します。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. CA の公開証明書を各ワーカー ノードに送信します。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 各ワーカー ノードで、CA の公開証明書をトラストストアとキーストアに追加します。 次に、ワーカー ノード自体の署名済み証明書をキーストアに追加します

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>SSL を使用してブローカーを再起動するように Kafka 構成を更新する

キーストアとトラストストアを使用して各 Kafka ブローカーを設定し、適切な証明書をインポートしました。 次に、Ambari を使用して関連の Kafka 構成プロパティを変更して、Kafka ブローカーを再起動します。

構成の変更を完了するために、次の手順を実行します。

1. Azure portal にサインインし、ご利用の Azure HDInsight Apache Kafka クラスターを選択します。
1. **[クラスター ダッシュボード]** の **[Ambari ホーム]** をクリックして、Ambari UI に移動します。
1. **[Kafka Broker]/(Kafka ブローカー/)** で、**リスナー** プロパティを `PLAINTEXT://localhost:9092,SSL://localhost:9093` に設定します。
1. **[Advanced kafka-broker]\(Kafka ブローカーの高度な設定\)** で **security.inter.broker.protocol** プロパティを `SSL` に設定します。

    ![Ambari での Kafka ssl 構成プロパティの編集](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. **Custom kafka-broker\(カスタム kafka ブローカー)** で **ssl.client.auth** プロパティを `required` に設定します。 この手順は、認証と暗号化を設定する場合にのみ必要です。

    ![Ambari での kafka ssl 構成プロパティの編集](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. **[Advanced kafka-env]\(kafka-env の詳細\)** 下で、 **[kafka-env template]\(kafka-env テンプレート\)** プロパティの末尾に次の行を追加します。

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Ambari 上で kafka-env テンプレート プロパティを編集する](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. すべての Kafka ブローカーを再起動します。
1. プロデューサーとコンシューマーのオプションを使用して管理者クライアントを起動し、プロデューサーとコンシューマーの両方がポート 9093 上で動作していることを確認します。

## <a name="client-setup-with-authentication"></a>クライアントのセットアップ (認証を使用)

> [!Note]
> 次の手順は、SSL 暗号化と認証の "**両方**" を設定する場合にのみ必要です。 暗号化のみを設定する場合は、「[クライアントのセットアップ (認証なし)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)」に進んでください。

次の手順を実行して、クライアントのセットアップを完了します。

1. クライアント コンピューター (スタンバイ ヘッド ノード) にサインインします。
1. java キーストアを作成し、ブローカーの署名証明書を取得します。 証明書を CA が実行されている VM にコピーします。
1. CA コンピューター (アクティブ ヘッド ノード) に切り替えて、クライアント証明書に署名します。
1. クライアント コンピューター (スタンバイ ヘッド ノード) に移動して、`~/ssl` フォルダーに移動します。 署名証明書をクライアント コンピューターにコピーします。

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (active head node) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (standby head node), navigate to ~/ssl folder and copy signed cert from the CA (active head node) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

最後に、`cat client-ssl-auth.properties` コマンドを使用してファイル `client-ssl-auth.properties` を表示します。 このファイルには、次の行が含まれています。

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>クライアントのセットアップ (認証なし)

認証が必要ない場合、次の手順を実行して SSL 暗号化のみ設定します。

1. クライアント コンピューター (hn1) にサインインし、`~/ssl` フォルダーに移動します。
1. CA コンピューター (wn0) からクライアント コンピューターに署名証明書をコピーします。
1. トラストストアに CA 証明書をインポートします。
1. キーストアに CA 証明書をインポートします。

これらの手順は、次のコード スニペットで示されています。

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

最後に、`cat client-ssl-auth.properties` コマンドを使用してファイル `client-ssl-auth.properties` を表示します。 このファイルには、次の行が含まれています。

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>次のステップ

* [HDInsight での Apache Kafka とは](apache-kafka-introduction.md)
