---
title: Azure Cosmos DB Emulator 証明書をエクスポートする
description: Windows 証明書ストアを使用しない言語とランタイムで開発する場合は、TLS/SSL 証明書をエクスポートして管理する必要があります。 この記事では詳しい手順について説明します。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200940"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java、Python、および Node.js で使用する Azure Cosmos DB Emulator 証明書のエクスポート

[**Emulator をダウンロードする**](https://aka.ms/cosmosdb-emulator)

Azure Cosmos DB Emulator には、開発の目的で Azure Cosmos DB サービスをエミュレートするローカル環境 (TLS 接続の使用を含む) が用意されています。 この記事では、独自の[証明書ストア](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)を使用する Java、[ソケット ラッパー](https://docs.python.org/2/library/ssl.html)を使用する Python、[tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) を使用する Node.js など、Windows 証明書ストアと統合されていない言語とランタイムで使用するために TLS/SSL 証明書をエクスポートする方法について説明します。 このエミュレーターの詳細については、「[開発とテストでの Azure Cosmos DB Emulator の使用](./local-emulator.md)」をご覧ください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 証明書のローテーション
> * TLS/SSL 証明書のエクスポート
> * Java、Python、および Node.js の証明書を使用する方法の学習

## <a name="certification-rotation"></a>証明書のローテーション

Azure Cosmos DB ローカル エミュレーターの証明書は、エミュレーターを初めて実行したときに生成されます。 証明書は 2 つあります。 1 つはローカル エミュレーターに接続するために使用し、もう 1 つはエミュレーター内のシークレットを管理するために使用します。 エクスポートする証明書は、"DocumentDBEmulatorCertificate" というフレンドリ名の接続証明書です。

どちらの証明書も、Windows トレイで実行されている Azure Cosmos DB エミュレーターから、次に示す **[データのリセット]** をクリックすることで生成し直すことができます。 証明書を Java 証明書ストアにインストールしたり、その他の場所で使用したりしていた場合に、証明書を生成し直したときは、元の証明書を更新する必要があります。更新を行わないと、アプリケーションはローカル エミュレーターに接続しなくなります。

![Azure Cosmos DB ローカル エミュレーターの [データのリセット]](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Azure Cosmos DB TLS/SSL 証明書をエクスポートする方法

1. certlm.msc を実行して Windows 証明書マネージャーを起動します。次に、Personal フォルダー、Certificates フォルダーの順に移動して、**DocumentDbEmulatorCertificate** というフレンドリ名の証明書を開きます。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 1.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. **[詳細]** 、 **[OK]** の順にクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 2.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. **[ファイルへコピー...]** をクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 3.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. **[次へ]** をクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 4.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. **[No, do not export private key (いいえ、秘密キーをエクスポートしません)]** をクリックしてから、 **[次へ]** をクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 5.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. **[Base 64 encoded X.509 (.CER)]** をクリックしてから、 **[次へ]** をクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 6.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. 証明書に名前を付けます。 ここでは「**documentdbemulatorcert**」と入力し、 **[次へ]** をクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 7.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. **[完了]** をクリックします。

    ![Azure Cosmos DB ローカル エミュレーターのエクスポート手順 8.](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Java で証明書を使用する方法

Java クライアントが使用される Java アプリケーションまたは MongoDB アプリケーションを実行している場合は、`-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` フラグを渡すよりも、既定の Java 証明書ストアに証明書をインストールする方が簡単です。 たとえば、付属の Java デモ アプリケーション (`https://localhost:8081/_explorer/index.html`) では、既定の証明書ストアを使用します。

X.509 証明書を既定の Java 証明書ストアにインポートする場合は、「[証明書を Java CA 証明書ストアに追加する方法](https://docs.microsoft.com/azure/java-add-certificate-ca-store)」の手順に従ってください。 keytool を実行する際には %JAVA_HOME% ディレクトリで作業することに注意してください。

"CosmosDBEmulatorCertificate" TLS/SSL 証明書がインストールされると、アプリケーションはローカルの Azure Cosmos DB Emulator に接続し、それを使用できるようになります。 依然として問題がある場合は、記事「[Debugging SSL/TLS Connections (SSL/TLS 接続のデバッグ)](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)」に従ってください。 多くの場合、証明書が %JAVA_HOME%/jre/lib/security/cacerts ストアにインストールされていません。 たとえば、複数のバージョンの Java をインストールしてある場合、更新したストアとは異なる cacerts ストアがアプリケーションによって使用されている可能性があります。

## <a name="how-to-use-the-certificate-in-python"></a>Python で証明書を使用する方法

既定では、SQL API 用の [Python SDK (バージョン 2.0.0 以降)](sql-api-sdk-python.md) は、ローカル エミュレーターに接続するときに TLS/SSL 証明書を使用しようとしません。 ただし、TLS 検証を使用したい場合は、[Python ソケット ラッパー](https://docs.python.org/2/library/ssl.html) のドキュメントの例に従うことができます。

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js で証明書を使用する方法

既定では、SQL API 用の [Node.js SDK (バージョン 1.10.1 以降)](sql-api-sdk-node.md) は、ローカル エミュレーターに接続するときに TLS/SSL 証明書を使用しようとしません。 ただし、TLS 検証を使用したい場合は、[Node.js のドキュメント](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)の例に従うことができます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * 証明書のローテーション
> * TLS/SSL 証明書のエクスポート
> * Java、Python、および Node.js の証明書を使用する方法の学習

これで、概念セクションに進み、Azure Cosmos DB の詳細について学習できるようになりました。 

> [!div class="nextstepaction"]
>[Azure Cosmos DB の調整可能なデータの一貫性レベル](../cosmos-db/consistency-levels.md)
