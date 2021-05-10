---
title: Azure Application Gateway での相互認証のトラブルシューティング
description: Application Gateway での相互認証をトラブルシューティングする方法を説明します
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230933"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Application Gateway での相互認証エラーのトラブルシューティング (プレビュー)

Application Gateway を使用する場合の相互認証での問題をトラブルシューティングする方法を説明します。 

## <a name="overview"></a>概要 

Application Gateway で相互認証を構成した後、相互認証を使用しようとすると、複数のエラーが表示される場合があります。 エラーの一般的な原因には、次のようなものがあります。

* ルート CA 証明書のない証明書または証明書チェーンをアップロードした
* 複数のルート CA 証明書がある証明書チェーンをアップロードした
* CA 証明書がないリーフ証明書のみを含む証明書チェーンをアップロードした 
* 発行者 DN の不一致による検証エラー  

ここでは、発生する可能性のあるさまざまなシナリオと、それらのシナリオをトラブルシューティングする方法について説明します。 その後、エラー コードを示し、相互認証によって発生する可能性がある特定のエラー コードの一般的な原因を説明します。 

## <a name="scenario-troubleshooting---configuration-problems"></a>シナリオのトラブルシューティング - 構成の問題
相互認証を構成しようとするときに直面する可能性がある、いくつかのシナリオがあります。 ここでは、よくある落とし穴のいくつかをトラブルシューティングする方法について説明します。 

### <a name="self-signed-certificate"></a>自己署名証明書

#### <a name="problem"></a>問題 

アップロードしたクライアント証明書は自己署名証明書であり、エラー コード ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate が生成されます。

#### <a name="solution"></a>解決策 

使用している自己署名証明書に *BasicConstraintsOid* = "2.5.29.19" という拡張機能があることを再確認します。これは、サブジェクトが CA として機能できることを示します。 これにより、使用される証明書が CA 証明書であることになります。 自己署名クライアント証明書を生成する方法の詳細については、[信頼されたクライアント証明書](./mutual-authentication-certificate-management.md)に関するページを参照してください。

## <a name="scenario-troubleshooting---connectivity-problems"></a>シナリオのトラブルシューティング - 接続の問題

問題なく相互認証を構成できても、アプリケーション ゲートウェイに要求を送信するときに問題が発生する可能性があります。 以下のセクションでは、いくつかの一般的な問題と解決策について説明します。 *sslClientVerify* プロパティは、アプリケーション ゲートウェイのアクセス ログで確認できます。 

### <a name="sslclientverify-is-none"></a>SslClientVerify が NONE

#### <a name="problem"></a>問題 

アクセス ログに、*sslClientVerify* プロパティが "NONE" と表示されています。 

#### <a name="solution"></a>解決策 

これは、クライアントが Application Gateway に要求を送信するときにクライアント証明書を送信しない場合に表示されます。 これは、Application Gateway に要求を送信しているクライアントが、クライアント証明書を使用するように正しく構成されていない場合に発生することがあります。 Application Gateway でクライアント認証の設定が想定どおりに動作しているかどうかを確認する方法の 1 つは、次の OpenSSL コマンドを使用することです。

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert` フラグはリーフ証明書であり、`-key` フラグはクライアントの秘密キー ファイルです。 

OpenSSL の `s_client` コマンドの使用方法の詳細については、[マニュアル ページ](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html)を参照してください。

### <a name="sslclientverify-is-failed"></a>SslClientVerify が FAILED

#### <a name="problem"></a>問題

アクセス ログに、*sslClientVerify* プロパティが "FAILED" と表示されています。 

#### <a name="solution"></a>解決策

アクセス ログ内のエラーには、さまざまな原因が考えられます。 エラーの一般的な原因の一覧を次に示します。
* **発行者証明書を取得できない:** クライアント証明書の発行者証明書が見つかりませんでした。 これは通常、信頼されたクライアント CA 証明書チェーンが Application Gateway で完全ではないことを意味します。 Application Gateway にアップロードされた、信頼されたクライアント CA 証明書チェーンが完全であることを確認してください。  
* **ローカル発行者証明書を取得できない:** 発行者証明書を取得できない場合と同様に、クライアント証明書の発行者証明書が見つかりませんでした。 これは通常、信頼されたクライアント CA 証明書チェーンが Application Gateway で完全ではないことを意味します。 Application Gateway にアップロードされた、信頼されたクライアント CA 証明書チェーンが完全であることを確認してください。
* **最初の証明書を確認できない:** クライアント証明書を確認できません。 このエラーは、クライアントがリーフ証明書のみを提示し、その発行者が信頼されていない場合に限って発生します。 Application Gateway にアップロードされた、信頼されたクライアント CA 証明書チェーンが完全であることを確認してください。
* **クライアント証明書の発行者を確認できない:** このエラーは、構成 *VerifyClientCertIssuerDN* が true に設定されている場合に発生します。 これは通常、クライアント証明書の発行者 DN が、顧客によってアップロードされた信頼されたクライアント CA 証明書チェーンから抽出された *ClientCertificateIssuerDN* と一致しない場合に発生します。 Application Gateway による *ClientCertificateIssuerDN* の抽出方法の詳細については、[Application Gateway による発行者 DN の抽出](./mutual-authentication-overview.md#verify-client-certificate-dn)に関するセクションを参照してください。 ベスト プラクティスとして、ファイルごとに 1 つの証明書チェーンを Application Gateway にアップロードするようにしてください。 

信頼されたクライアント CA 証明書チェーン全体を抽出して Application Gateway にアップロードする方法の詳細については、[信頼されたクライアント CA 証明書チェーンの抽出方法](./mutual-authentication-certificate-management.md)に関するページを参照してください。

## <a name="error-code-troubleshooting"></a>エラー コードのトラブルシューティング
次のいずれかのエラー コードが表示されている場合は、直面している可能性のある問題を解決するために推奨される解決策がいくつかあります。 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>エラー コード: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>原因

見つからない証明書データがあります。 アップロードされた証明書が、証明書データを含まない空のファイルである可能性があります。 

#### <a name="solution"></a>解決策

アップロードした証明書ファイルに不足しているデータがないことを確認します。 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>エラー コード: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>原因

証明書チェーンに秘密キーがあります。 証明書チェーンに秘密キーを含めることはできません。 

#### <a name="solution"></a>解決策

アップロードした証明書チェーンを再確認し、チェーンの一部である秘密キーを削除します。 秘密キーを含まないチェーンを再アップロードします。 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>エラー コード: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>原因

このエラー コードの背後には、2 つの原因が考えられます。
1. チェーンが正しい形式で提示されていないため、解析に失敗しました。 Application Gateway では、証明書チェーンが PEM 形式であることが想定されています。また、個々の証明書データが区切られていることも想定されています。 
2. 解析するものが見つかりませんでした。 アップロードされたファイルには、区切り記号だけが含まれ、証明書データが含まれていない可能性があります。 

#### <a name="solution"></a>解決策

このエラーの原因に応じて、2 つの解決策が考えられます。 
* アップロードした証明書チェーンが正しい形式 (PEM) であり、証明書データが正しく区切られていたことを確認します。 
* アップロードした証明書ファイルに、区切り記号だけでなく証明書データが含まれていることを確認します。 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>エラー コード: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>原因

アップロードした証明書に、CA 証明書がないリーフ証明書だけが含まれていました。 CA 証明書とリーフ証明書を含む証明書チェーンをアップロードしても、リーフ証明書は無視されるだけなので問題ありませんが、証明書には CA が必要です。 

#### <a name="solution"></a>解決策 

アップロードした証明書チェーンにリーフ証明書以外のものが含まれていることを再確認します。 *BasicConstraintsOid* = "2.5.29.19" 拡張機能があり、サブジェクトが CA として機能できることを示している必要があります。

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>エラー コード: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>原因

証明書チェーンに複数のルート CA 証明書が含まれています。"*または*"、ルート CA 証明書が含まれていません。 

#### <a name="solution"></a>解決策

アップロードする証明書には、ルート CA 証明書が 1 つだけ含まれている必要があります (ただし、中間 CA 証明書は必要な数だけ含めることができます)。


