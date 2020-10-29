---
title: Azure Database for MariaDB での証明書のローテーション
description: Azure Database for MariaDB に影響するルート証明書の今後の変更について説明します
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 4744b974cd20c15d8abf22f52b64b8d3dc5a7f55
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743013"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Azure Database for MariaDB のルート CA の変更について

Azure Database for MariaDB によって、[データベース サーバーに接続する](concepts-connectivity-architecture.md)ために使用する、SSL で有効にされるクライアント アプリケーションやドライバー用のルート証明書が変更されます。 現在使用できるルート証明書は、標準メンテナンスおよびセキュリティのベスト プラクティスの一部として、2021 年 2 月 15 日 (2021/02/15) に有効期限が切れます。 この記事では、予定されている変更、影響を受けるリソース、アプリケーションでデータベース サーバーへの接続を確実に維持するために必要な手順について、詳しく説明します。

>[!NOTE]
> お客様からのフィードバックに基づいて、既存の Baltimore Root CA のルート証明書の非推奨を、2020 年 10 月 26 日から 2021 年 2 月 15 日まで延長しました。 この延長により、ユーザーが影響を受ける場合に、クライアントの変更を実装するのに十分なリード タイムを提供できるのではないかと考えています。

## <a name="what-update-is-going-to-happen"></a>予定されている更新

アプリケーションでは、安全に接続するために、信頼された証明機関 (CA) の証明書ファイルから生成されたローカル証明書ファイルが使用される場合があります。 現在、お客様が Azure Database for MariaDB サーバーに接続するには、[こちら](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)にある定義済みの証明書のみを使用できます。 ただし、[証明機関 (CA) ブラウザー フォーラム](https://cabforum.org/)から最近、CA ベンダーによって発行された複数の証明書が準拠していないことを示すレポートが公開されました。

業界のコンプライアンス要件に従い、CA ベンダーは準拠していない CA の CA 証明書の取り消しを始めており、サーバーでは、準拠している CA で発行され、それらの準拠している CA からの CA 証明書によって署名された証明書が使用される必要があります。 現在、Azure Database for MariaDB によってこれらの非準拠証明書の 1 つが使用されており、クライアント アプリケーションではそれを使用して SSL 接続が検証されているため、MariaDB サーバーへの潜在的な影響を最小限に抑えるため、以下に説明する適切なアクションが確実に実行されるようにする必要があります。


新しい証明書は、2021 年 2 月 15 日 (2021/02/15) から使用されるようになります。MySQL クライアントからの接続時に、サーバー証明書の CA 検証または完全検証 (sslmode=verify-ca または sslmode=verify-full) を使用している場合は、2021 年 2 月 15 日 (2021/02/15) より前にアプリケーションの構成を更新する必要があります。

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>データベースが影響を受けるかどうかを確認する方法

SSL/TLS を使用し、ルート証明書を検証しているすべてのアプリケーションで、ルート証明書を更新する必要があります。 お使いの接続で、接続文字列を確認するとルート証明書が検証されているかどうかを、確認することができます。
-   接続文字列に `sslmode=verify-ca` または `sslmode=verify-identity` が含まれている場合は、証明書を更新する必要があります。
-   接続文字列に `sslmode=disable`、`sslmode=allow`、`sslmode=prefer`、または `sslmode=require` が含まれている場合は、証明書を更新する必要はありません。 
-   接続文字列で sslmode が指定されていない場合は、証明書を更新する必要はありません。

接続文字列が抽象化されるクライアントを使用している場合、証明書が検証されているかどうかを確認するには、クライアントのドキュメントを参照してください。
Azure Database for MariaDB の sslmode については、[SSL モードの説明](concepts-ssl-connection-security.md#default-settings)に関する記事をご確認ください。

証明書の予期しない取り消しによってアプリケーションの可用性が中断されないようにするには、または取り消された証明書を更新するには、「 [**接続を維持するために必要な作業**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)」セクションを参照してください。

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>接続を維持するために必要な作業

証明書の予期しない取り消しによってアプリケーションの可用性が中断されないようにするには、または取り消された証明書を更新するには、以下の手順のようにします。 考え方は、現在の証明書と新しい証明書を組み合わせた新しい *.pem* ファイルを作成し、SSL 証明書の検証の間に、許可された値の 1 つを使用する、というものです。 次の手順を参照してください。

*   以下のリンクから、 **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の CA をダウンロードします。
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方の証明書が含まれる、結合された CA 証明書ストアを生成します。
    *   Java (MariaDB Connector/J) ユーザーの場合、以下を実行します。

          ```console
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          次に、元のキーストア ファイルを、新しく生成されたものに置き換えます。
        *   System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file"); 
        *   System.setProperty("javax.net.ssl.trustStorePassword","password");
    *   .NET (MariaDB Connector/NET、MariaDBConnector) ユーザーの場合は、 **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方が Windows 証明書ストアの信頼されたルート証明機関に存在することを確認します。 いずれかの証明書が存在しない場合は、不足している証明書をインポートします。

        ![Azure Database for MariaDB .net 証明書](media/overview/netconnecter-cert.png)

    *   SSL_CERT_DIR を使用する Linux 上の .NET ユーザーの場合は、 **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方が SSL_CERT_DIR によって示されるディレクトリに存在することを確認します。 いずれかの証明書が存在しない場合は、不足している証明書ファイルを作成します。

    *   その他の (MariaDB Client、MariaDB Workbench、C、C++、Go、Python、Ruby、PHP、NodeJS、Perl、Swift) ユーザーの場合は、次の形式のような 2 つの CA 証明書ファイルをマージできます</b>

        </br>-----BEGIN CERTIFICATE-----
 </br>(ルート CA1: BaltimoreCyberTrustRoot.crt.pem)
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(ルート CA2: DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   元のルート CA pem ファイルを、結合されたルート CA ファイルに置き換えて、アプリケーションやクライアントを再起動します。
*   将来的に、サーバー側に新しい証明書がデプロイされた後は、CA pem ファイルを DigiCertGlobalRootG2.crt.pem に変更することができます。

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>証明書を更新しない場合に可能性のある影響
ここで説明されているように、Azure Database for MariaDB で発行された証明書を使用している場合は、データベースに到達できなくなるため、アプリケーションの可用性が中断される可能性があります。 アプリケーションによっては、次のようなさまざまなエラー メッセージが表示される場合があります。
*   無効な証明書/失効した証明書
*   接続がタイムアウトしました

> [!NOTE]
> 証明書の変更が行われるまで、 **Baltimore 証明書** を破棄または変更しないでください。 変更が完了すると、Microsoft からメッセージが送信されます。その後、Baltimore 証明書を安全に削除できます。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1.SSL/TLS を使用していない場合でも、ルート CA を更新する必要がありますか?
SSL/TLS を使用していない場合は、何も行う必要は必要ありません。 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2.SSL/TLS を使用している場合、ルート CA を更新するには、データベース サーバーを再起動する必要がありますか?
いいえ。新しい証明書を使い始めるために、データベース サーバーを再起動する必要はありません。 証明書の更新はクライアント側の変更であり、確実にデータベース サーバーに接続できるよう、受信クライアント接続で新しい証明書を使用する必要があります。

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-february-15-2021-02152021"></a>3.2021 年 2 月 15 日 (2021/02/15) までにルート証明書を更新しないとどうなりますか?
2021 年 2 月 15 日 (2021/02/15) までにルート証明書を更新しない場合、SSL/TLS を使用して接続し、ルート証明書に対する検証を実行するアプリケーションは、MariaDB データベース サーバーと通信できなくなり、アプリケーションで MariaDB データベース サーバーへの接続に関する問題が発生します。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4.Azure Database for MariaDB で App Service を使用している場合、どのような影響がありますか?
Azure App Services で Azure Database for MariaDB に接続している場合、アプリケーションでの SSL の使用方法により、2 つのシナリオが考えられます。
*   この新しい証明書は、プラットフォーム レベルで App Service に追加されています。 App Service プラットフォームに含まれる SSL 証明書をアプリケーションで使用している場合は、何もする必要はありません。
*   SSL 証明書ファイルへのパスをコードに明示的に含めている場合は、新しい証明書をダウンロードし、新しい証明書を使用するようにコードを更新する必要があります。このシナリオの良い例は、[App Service ドキュメント](/app-service/tutorial-multi-container-app#configure-database-variables-in-wordpress.md)で共有されているように、App Service でカスタム コンテナーを使用する場合です。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5.Azure Database for MariaDB で Azure Kubernetes Services (AKS) を使用している場合、どのような影響がありますか?
Azure Kubernetes Services (AKS) を使用して Azure Database for MariaDB に接続しようとしている場合は、専用の顧客ホスト環境からのアクセスに似ています。 [こちら](../aks/ingress-own-tls.md)の手順を参照してください。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6.Azure Data Factory を使用して Azure Database for MariaDB に接続している場合、どのような影響がありますか?
Azure Integration Runtime を使用しているコネクタでは、Azure でホストされる環境内の Windows 証明書ストアの証明書が利用されています。 これらの証明書は、新しく適用される証明書と既に互換性があるため、何もする必要はありません。

接続文字列に SSL 証明書ファイルへのパスを明示的に含めるセルフホステッド統合ランタイムを使用しているコネクタでは、[新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)をダウンロードし、それを使用するように接続文字列を更新する必要があります。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.この変更では、データベース サーバーのメンテナンス ダウンタイムを計画する必要がありますか?
いいえ。 ここでの変更は、データベース サーバーに接続するためにクライアント側でのみ行うものなので、この変更のためにデータベース サーバーでメンテナンス ダウンタイムは必要ありません。

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8.2021 年 2 月 15 日 (2021/02/15) より前に、この変更のためのダウンタイムをスケジュールできない場合はどうすればよいですか?
修正に関する[こちら](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)のセクションで説明されているように、サーバーへの接続に使用されるクライアントで証明書情報を更新する必要があるため、この場合、サーバーのダウンタイムは必要ありません。

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9.2021 年 2 月 15 日 (2021/02/15) より後に新しいサーバーを作成する場合、影響はありますか?
2021 年 2 月 15 日 (2021/02/15) より後に作成されるサーバーに対しては、アプリケーションが SSL を使用して接続するために新しく発行された証明書を使用できます。

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10.Microsoft はどのくらいの頻度で証明書を更新しますか、または有効期限ポリシーはどのようなものですか?
Azure Database for MariaDB によって使用されるこれらの証明書は、信頼された証明機関 (CA) によって提供されます。 そのため、Azure Database for MariaDB でのこれらの証明書のサポートは、CA によるこれらの証明書のサポートに関連付けられています。 ただし、今回のケースのように、事前に定義されたこれらの証明書に予期しないバグが存在する可能性があり、その場合はできるだけ早く修正する必要があります。

### <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11. 読み取りレプリカを使用している場合、この更新を行う必要があるのはソース サーバーだけですか? または、読み取りレプリカでも必要ですか?
この更新はクライアント側の変更であるため、レプリカ サーバーからデータを読み取るためにクライアントを使用している場合は、それらのクライアントにも変更を適用する必要があります。

### <a name="12-if-i-am-using-data-in-replication-do-i-need-to-perform-any-action"></a>12.データイン レプリケーションを使用している場合は、何かアクションを実行する必要がありますか?
[データイン レプリケーション](concepts-data-in-replication.md)を使用して Azure Database for MySQL に接続している場合は、次の 2 つの点を考慮する必要があります。
*   仮想マシン (オンプレミスまたは Azure 仮想マシン) から Azure Database for MySQL へのデータ レプリケーションの場合は、レプリカを作成するために SSL が使用されているかどうかを確認する必要があります。 **SHOW SLAVE STATUS** を実行し、次の設定を確認します。  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    CA_file、SSL_Cert、SSL_Key に対して証明書が提供されていることがわかった場合は、[新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)を追加してファイルを更新する必要があります。

*   2 つの Azure Database for MySQL 間のデータ レプリケーションの場合は、 **CALL mysql.az_replication_change_master** を実行し、最後のパラメーター [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) として新しいデュアル ルート証明書を指定することによってレプリカをリセットする必要があります。

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13.SSL が使用されているかどうかを確認するためのサーバー側クエリはありますか?
サーバーへの接続に SSL 接続を使用しているかどうかを確認するには、[SSL の検証](howto-configure-ssl.md#verify-the-ssl-connection)に関する記事を参照してください。

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14. 証明書ファイルに DigiCertGlobalRootG2 が既に含まれている場合、必要なアクションはありますか?
いいえ。 証明書ファイルに **DigiCertGlobalRootG2** が既に含まれている場合、必要なアクションはありません。

### <a name="15-what-if-i-have-further-questions"></a>15. さらに質問がある場合はどうすればよいですか?
質問がある場合は、[Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com) でコミュニティの専門家から回答を得ることができます。 サポート プランに加入していて技術的な支援が必要な場合は、[お問い合わせください](mailto:AzureDatabaseformariadb@service.microsoft.com)。
