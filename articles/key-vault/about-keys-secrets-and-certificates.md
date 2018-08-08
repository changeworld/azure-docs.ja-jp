---
title: キー、シークレット、証明書について
description: REST インターフェイスの概要と KV 開発者の詳細です
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 8597b2d995b68e9ccff9b856b2ef6bd325cd2439
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359191"
---
# <a name="about-keys-secrets-and-certificates"></a>キー、シークレット、証明書について
開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内に暗号化キーを保存して使用できます。 Key Vault は複数のキーの種類とアルゴリズムをサポートし、価値の高いキーにハードウェア セキュリティ モジュールを使用できるようにします。 さらに、Key Vault では、ユーザーはシークレットを安全に保管できます。 シークレットは、特定のセマンティクスを持たない限られたサイズのオクテット オブジェクトです。 Key Vault は、キーとシークレットを基に構築された証明書もサポートし、自動更新機能を追加します。

Azure Key Vault の一般的な情報については、「[Azure Key Vault とは](/azure/key-vault/key-vault-whatis)」をご覧ください。

**Key Vault の一般的な詳細情報**

-   [標準のサポート](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [データ型](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [オブジェクト、識別子、バージョン管理](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**キーについて**

-   [キーとキーの種類](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [RSA アルゴリズム](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [RSA-HSM アルゴリズム](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [暗号化による保護](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [キーの操作](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [キーの属性](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [キーのタグ](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**シークレットについて** 

-   [シークレットの操作](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [シークレットの属性](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [シークレットのタグ](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [シークレットのアクセス制御](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**証明書について**

-   [証明書の構成](#BKMK_CompositionOfCertificate)  
-   [証明書の属性とタグ](#BKMK_CertificateAttributesAndTags)  
-   [証明書のポリシー](#BKMK_CertificatePolicy)  
-   [証明書の発行者](#BKMK_CertificateIssuer)  
-   [証明書の連絡先](#BKMK_CertificateContacts)  
-   [証明書のアクセス制御](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Key Vault の一般的な詳細情報

以下のセクションでは、Azure Key Vault サービスの実装に該当する一般的な情報を提供します。

###  <a name="BKMK_Standards"></a> 標準のサポート

JavaScript Object Notation (JSON) および JavaScript Object Signing and Encryption (JOSE) の仕様は、重要な背景情報です。  

-   [JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> データ型

キー、暗号化、および署名に関連するデータ型については、[JOSE の仕様](#BKMK_Standards)をご覧ください。  

-   **algorithm** - キーの操作に対してサポートされるアルゴリズムです (RSA1_5 など)  
-   **ciphertext-value** - Base64URL を使用してエンコードされた暗号テキスト オクテットです  
-   **digest-value** - Base64URL を使用してエンコードされたハッシュ アルゴリズムの出力です  
-   **key-type** - サポートされているキーの種類の 1 つです (RSA など)  
-   **plaintext-value** - Base64URL を使用してエンコードされたプレーンテキスト オクテットです  
-   **signature-value** - Base64URL を使用してエンコードされた署名アルゴリズムの出力です  
-   **base64URL** - Base64URL [RFC4648] でエンコードされたバイナリ値です  
-   **boolean** - true または false です  
-   **Identity** - Azure Active Directory (AAD) からの ID です。  
-   **IntDate** - 1970-01-01T0:0:0Z UTC から指定された UTC 日時までの秒数を表す JSON 10 進値です。 一般的および UTC 固有の日付/時刻に関する詳細については、[RFC3339] をご覧ください。  

###  <a name="BKMK_ObjId"></a> オブジェクト、識別子、バージョン管理

Azure Key Vault に格納されるオブジェクトは、オブジェクトの新しいインスタンスが作成されるたびにバージョンを保持され、各バージョンは一意の識別子と URL を持ちます。 オブジェクトが最初に作成されるときに、オブジェクトに一意のバージョン識別子が指定され、オブジェクトの現在のバージョンとしてマークされます。 同じオブジェクト名の新しいインスタンスが作成されると、新しいオブジェクトに一意のバージョン識別子が与えられ、現在のバージョンになります。  

Azure Key Vault 内のオブジェクトは、現在の識別子またはバージョン固有の識別子を使用してアドレス指定できます。 たとえば、キーの名前を "MasterKey" とすると、現在の識別子を指定して操作を実行すると、システムは使用可能な最新のバージョンを使用します。 バージョン固有の識別子を指定して操作を実行すると、システムはオブジェクトの特定のバージョンを使用します。  

オブジェクトは、URL を使用して Azure Key Vault 内で一意に識別され、地理的な場所に関係なく、システム内の 2 つのオブジェクトが同じ URL を持つことはありません。 オブジェクトへの完全な URL はオブジェクト識別子と呼ばれ、Key Vault を示すプレフィックス部分、オブジェクトの種類、ユーザー指定のオブジェクト名、およびオブジェクトのバージョンで構成されます。 オブジェクト名は大文字と小文字が区別されず、変更できません。 オブジェクトのバージョンを含まない識別子は、ベース識別子と呼ばれます。  

詳しくは、「[Authentication, requests, and responses](authentication-requests-and-responses.md)」(認証、要求、応答) をご覧ください。

オブジェクト識別子の一般的な形式は次のとおりです。  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

各値の説明:  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault サービスでのキー コンテナーの名前。<br /><br /> キー コンテナーの名前はユーザーが選択し、グローバルに一意です。<br /><br /> キー コンテナーの名前は、0 ～ 9、a ～ z、A ～ Z、- のみを使った 3 ～ 24 文字の文字列である必要があります。|  
|`object-type`|オブジェクトの型。"keys" または "secrets"。|  
|`object-name`|`object-name` は、ユーザーが指定する名前で、キー コンテナー内で一意である必要があります。 名前は、0 ～ 9、a ～ z、A ～ Z、- のみを使った 1 ～ 127 文字の文字列である必要があります。|  
|`object-version`|`object-version` はシステムが生成し、オブジェクトの一意のバージョンに対応するために必要に応じて使用される 32 文字の文字列識別子です。|  

## <a name="key-vault-keys"></a>Key Vault のキー

###  <a name="BKMK_KeyTypes"></a> キーとキーの種類

Azure Key Vault の暗号化キーは、JSON Web Key (JWK) オブジェクトとして表されます。 JWK/JWA の基本仕様は、Azure Key Vault の実装に固有のキーの種類を有効にするために拡張されます。たとえば、HSM ベンダー (Thales) 固有のパッケージングを使用する Azure Key Vault へのキーのインポートは、Azure Key Vault HSM でのみ使用可能であるため、キーの転送がセキュリティで保護されます。  

- **"ソフト" キー**: Key Vault によってソフトウェアで処理されるが、HSM 内のシステム キーを使って保存時に暗号化されるキー。 クライアントは、既存の RSA または EC キーをインポートするか、または Azure Key Vault による生成を要求できます。
- **"ハード" キー**: HSM (ハードウェア セキュリティ モジュール) で処理されるキー。 これらのキーは、Azure Key Vault HSM セキュリティ ワールドのいずれかで保護されます (分離を維持するために場所ごとにセキュリティ ワールドがあります)。 クライアントは、RSA または EC キーをインポートするか (ソフト形式で、または互換性のある HSM デバイスからエクスポートすることにより)、または Azure Key Vault に生成を要求できます。 このキーの種類は、HSM キー マテリアルを保持するために JWK 取得に T 属性を追加します。

     地理的境界について詳しくは、[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/privacy/)をご覧ください。  

Azure Key Vault では、RSA および楕円曲線キーのみがサポートされています。今後のリリースでは、対称タイプなどのその他の種類のキーもサポートされる可能性があります。

-   **EC**: "ソフト" 楕円曲線キー。
-   **EC-HSM**: "ハード" 楕円曲線キー。
-   **RSA**: "ソフト"RSA キー。
-   **RSA HSM**: "ハード" RSA キー。

Azure Key Vault は、2048、3072、4096 サイズの RSA キーと P-256、P-384、P-521 および P-256K タイプの楕円曲線キーをサポートします。

### <a name="BKMK_Cryptographic"></a> 暗号化による保護

Azure Key Vault が使う暗号化モジュールは、HSM でもソフトウェアでも、FIPS で検証されます。 FIPS モードで実行するために特別なことを行う必要はありません。 HSM で保護されたキーを**作成**または**インポート**する場合は、キーは FIPS 140-2 Level 2 以上に対して検証された HSM の内部で処理されることが保証されます。 ソフトウェアで保護されたキーを**作成**または**インポート**する場合は、FIPS 140-2 Level 1 以上に対して検証された暗号モジュールの内部で処理されます。 詳しくは、「[Keys and key types](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)」(キーとキーの種類) をご覧ください。

###  <a name="BKMK_ECAlgorithms"></a> EC アルゴリズム
 Azure Key Vault の EC および EC-HSM キーでは、次のアルゴリズム識別子がサポートされます。 

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - SHA-256 ダイジェストおよび P-256 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、[RFC7518] で説明されます。
-   **ES256SHA-256** - SHA-256 ダイジェストおよび P-256K 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、標準としての承認待ちです。
-   **ES384** - SHA-384 ダイジェストおよび P-384 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、[RFC7518] で説明されます。
-   **ES512** - SHA-512 ダイジェストおよび P-521 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、[RFC7518] で説明されます。

###  <a name="BKMK_RSAAlgorithms"></a> RSA アルゴリズム  
 Azure Key Vault の RSA および RSA-HSM キーでは、次のアルゴリズム識別子がサポートされます。  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY、ENCRYPT/DECRYPT

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] キー暗号化  
-   **RSA-OAEP** - Optimal Asymmetric Encryption Padding (OAEP) [RFC3447] を使用する RSAES。既定のパラメーターは RFC 3447 の Section A.2.1 で指定されています。 これらの既定パラメーターでは、SHA-1 のハッシュ関数と、SHA-1 による MGF1 のマスク生成関数が使用されます。  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** - SHA-256 を使用する RSASSA-PKCS-v1_5。 アプリケーション提供のダイジェスト値は SHA-256 を使用して計算され、長さは 32 バイトである必要があります。  
-   **RS384** - SHA-384 を使用する RSASSA-PKCS-v1_5。 アプリケーション提供のダイジェスト値は SHA-384 を使用して計算され、長さは 48 バイトである必要があります。  
-   **RS512** - SHA-512 を使用する RSASSA-PKCS-v1_5。 アプリケーション提供のダイジェスト値は SHA-512 を使用して計算され、長さは 64 バイトである必要があります。  
-   **RSNULL** - RFC2437 を参照。特定の TLS シナリオを有効にする特殊なユース ケース。  

###  <a name="BKMK_KeyOperations"></a> キーの操作

Azure Key Vault は、キー オブジェクトに対する次の操作をサポートします。  

-   **作成**: クライアントは、Azure Key Vault にキーを作成できます。 キーの値は、Azure Key Vault によって生成されて格納され、クライアントにはリリースされません。 Azure Key Vault では非対称 (および将来的には楕円曲線と対称) キーを作成できます。  
-   **インポート**: クライアントは、既存のキーを Azure Key Vault にインポートできます。 JWK コンストラクト内の複数のパッケージング方法を使用して、非対称 (および将来的には楕円曲線と対称) キーを Azure Key Vault にインポートできます。  
-   **更新**: 十分なアクセス許可を持つクライアントは、以前に Azure Key Vault に格納されたキーに関連付けられているメタデータ (キー属性) を変更できます。  
-   **削除**: 十分なアクセス許可を持つクライアントは、Azure Key Vault からキーを削除できます。  
-   **一覧表示**: クライアントは、特定の Azure Key Vault 内のすべてのキーを一覧表示できます。  
-   **バージョンの一覧表示**: クライアントは、特定の Azure Key Vault 内の特定のキーのすべてのバージョンを一覧表示できます。  
-   **取得**: クライアントは、Azure Key Vault 内の特定のキーの公開部分を取得できます。  
-   **バックアップ**: 保護された形式でキーをエクスポートします。  
-   **復元**: 以前にバックアップしたキーをインポートします。  

詳しくは、[キーの操作](/rest/api/keyvault/key-operations)に関するページをご覧ください。  

Azure Key Vault にキーが作成されたら、キーを使って次の暗号化操作を実行できます。  

-   **署名と検証**: Azure Key Vault は署名作成の一部として内容のハッシュをサポートしていないため、厳密には、この操作は "ハッシュの署名" または "ハッシュの検証" です。 アプリケーションは、署名されるデータをローカルにハッシュした後、Azure Key Vault にハッシュの署名を要求する必要があります。 "公開" キー マテリアルにアクセスできないアプリケーション用の便利な操作として、署名されたハッシュの検証がサポートされています。アプリケーションのパフォーマンスを最高にするため、検証操作はローカルに実行することをお勧めします。  
-   **キーの暗号化/ラッピング**: Azure Key Vault に格納されているキーを使って、別のキー (通常は、対称コンテンツ暗号化キー (CEK)) を保護できます。 Azure Key Vault 内のキーが非対称の場合、キーの暗号化が使用されます。たとえば、RSA-OAEP 操作と WRAPKEY/UNWRAPKEY 操作は、ENCRYPT/DECRYPT と同等です。 Azure Key Vault 内のキーが対称の場合は、キー ラッピング キーが使用されます (AES-KW など)。 "公開" キー マテリアルにアクセスできないアプリケーション用の便利な操作として、WRAPKEY 操作がサポートされています。アプリケーションのパフォーマンスを最高にするため、WRAPKEY 操作はローカルに実行することをお勧めします。  
-   **暗号化と復号化**: Azure Key Vault に格納されているキーを使って、データの単一のブロックを暗号化または復号化できます。ブロックのサイズは、キーの種類および選択されている暗号化アルゴリズムによって決まります。 "公開" キー マテリアルにアクセスできないアプリケーション用の便利な操作として、暗号化操作が提供されています。アプリケーションのパフォーマンスを最高にするため、暗号化操作はローカルに実行することをお勧めします。  

非対称キーを使用する WRAPKEY/UNWRAPKEY は、ENCRYPT/DECRYPT と同等なので余分に見えますが、個別の操作の使用は、これらの操作のセマンティックと承認を分離し、他のキーの種類がサービスによってサポートされている場合の整合性のために、重要であると考えられます。  

Azure Key Vault は EXPORT 操作をサポートしていません。キーがシステム内でプロビジョニングされた後、キーを抽出したり、キー マテリアルを変更したりすることはできません。  ただし、他のユース ケースのために、または Azure Key Vault で削除された後で、キーを必要とする Azure Key Vault のユーザーは、BACKUP および RESTORE 操作を使って、保護された形式でキーをエクスポート/インポートできます。 BACKUP 操作によって作成されたキーは、Azure Key Vault の外部では使用できません。 代わりに、複数の Azure Key Vault インスタンスに対して、IMPORT 操作を使用できます。  

ユーザーは、JWK オブジェクトの key_ops プロパティを使って、キー単位で、Azure Key Vault がサポートする暗号化操作を制限できます。  

JWK オブジェクトについて詳しくは、「[JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)」をご覧ください。  

###  <a name="BKMK_KeyAttributes"></a> キーの属性

キー マテリアルに加えて、次の属性を指定できます。 JSON 要求では、属性を指定しない場合であっても、attributes キーワードとかっこ { } は必要です。  

- *enabled*: boolean、省略可能、既定値は **true**。 キーが有効になっていて、暗号化操作に使用できるかどうかを指定します。 *enabled* 属性は、*nbf* および *exp* と組み合わせて使います。*nbf* と *exp* の間で操作が発生する場合、*enabled* が **true** に設定されている場合にのみ許可されます。 *nbf* / *exp* ウィンドウの外部での操作は、[特定の条件](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops)の下での特定の操作の種類を除き、自動的に禁止されます。
- *nbf*: IntDate、省略可能、既定値は現在。 *nbf* (not before: 開始日時) 属性は、[特定の条件](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops)の下での特定の操作の種類を除き、それより前にはキーを暗号化操作に使用してはならない日時を示します。 *nbf* 属性の処理には、現在の日時が、*nbf* 属性で指定されている開始日時以降でなければなりません。 Azure Key Vault では、クロックのスキューを考慮して、短い猶予 (通常は数分以下) を提供できます。 その値は、IntDate 値を含む数値でなければなりません。  
- *exp*: IntDate、省略可能、既定値は "無期限"。 *exp* (expiration time: 有効期限) 属性は、[特定の条件](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops)の下での特定の操作の種類を除き、それを過ぎたらキーを暗号化操作に使用してはならない日時を示します。 *exp* 属性の処理には、現在の日時が、*exp* 属性で指定されている有効期限日時以前でなければなりません。 Azure Key Vault では、クロックのスキューを考慮して、短い猶予 (通常は数分以下) を提供できます。 その値は、IntDate 値を含む数値でなければなりません。  

キー属性を含むすべての応答に含まれる追加の読み取り専用属性があります。  

- *created*: IntDate、省略可能。 *created* 属性は、このバージョンのキーが作成された日時を示します。 この属性が追加される前に作成されたキーについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。  
- *updated*: IntDate、省略可能。 *updated* 属性は、このバージョンのキーが更新された日時を示します。 この属性が追加される前に最後に更新されたキーについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。  

IntDate および他のデータ型について詳しくは、「[データ型](about-keys-secrets-and-certificates.md#BKMK_DataTypes)」をご覧ください。  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> 日付と時刻で制御される操作

*nbf* / *exp* ウィンドウの範囲外である、有効期間前および有効期間後のキーは、**復号化**、**ラップ解除**、**検証**操作には使用できます (403 Forbidden は返りません)。 有効期間前状態を使用する理由は、運用環境で使用する前にキーをテストできるようにすることです。 期限切れ状態を使用する理由は、キーが有効であったときに作成されたデータを回復できるようにすることです。 また、Key Vault のポリシーを使って、または *enabled* キー属性を **false** に更新することで、キーへのアクセスを無効にすることもできます。

データ型について詳しくは、「[データ型](about-keys-secrets-and-certificates.md#BKMK_DataTypes)」をご覧ください。

他の使用可能な属性について詳しくは、「[JSON Web Key (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)」をご覧ください。

### <a name="BKMK_Keytags"></a> キーのタグ

タグの形式で、アプリケーション固有の追加メタデータを指定できます。 Azure Key Vault は最大 15 個のタグをサポートし、それぞれが 256 文字の名前と 256 文字の値を持つことができます。  

>[!Note]
>呼び出し元は、そのオブジェクトの種類、キー、シークレット、または証明書に対して *list* または *get* アクセス許可を持っている場合、タグを読み取ることができます。

###  <a name="BKMK_KeyAccessControl"></a> キーのアクセス制御

Key Vault によって管理されているキーのアクセス制御は、キーのコンテナーとして機能する Key Vault のレベルで提供されます。 キーのアクセス制御ポリシーは、同じキー コンテナー内のシークレットに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成してキーを保持することができ、キーのセグメント化と管理に適切なシナリオを維持する必要があります。 キーのアクセス制御は、シークレットのアクセス制御に依存しません。  

コンテナーのキー アクセス制御エントリでは、ユーザー/サービス プリンシパルごとに、次のアクセス許可を付与できます。 これらのアクセス許可は、キー オブジェクトに対して許可される操作を厳密に反映しています。  

-   *create*: 新しいキーを作成します
-   *get*: キーの公開部分とその属性を読み取ります
-   *list*: キー コンテナーに格納されているキーまたはキーのバージョンを一覧表示します
-   *update*: キーの属性を更新します
-   *delete*: キー オブジェクトを削除します
-   *sign*: キーを使ってダイジェストに署名します
-   *verify*: キーを使ってダイジェストを検証します
-   *wrapKey*: キーを使って対称キーを保護します
-   *unwrapKey*: キーを使ってラップされた対称キーの保護を解除します
-   *encrypt*: キーを使って任意のバイト シーケンスを保護します
-   *decrypt*: キーを使ってバイト シーケンスの保護を解除します
-   *import*: キーをキー コンテナーにインポートします
-   *backup*: キー コンテナー内のキーをバックアップします
-   *restore*: バックアップしたキーをキー コンテナーに復元します
-   *all*: すべてのアクセス許可

## <a name="key-vault-secrets"></a>キー コンテナーのシークレット 

###  <a name="BKMK_WorkingWithSecrets"></a> シークレットの操作

Azure Key Vault のシークレットは、それぞれが最大サイズ 25 K バイトのオクテット シーケンスです。 Azure Key Vault サービスでは、シークレットに対してどのようなセマンティクスも提供されません。単に、データを受け取り、暗号化して格納し、後でシークレットを取得するために使用できるシークレットの識別子 "id" を返します。  

機密性の高いデータでは、クライアントは Azure Key Vault に格納されているデータに対して保護レイヤーの追加を検討する必要があります。たとえば、異なる保護キーを使用してデータを事前に暗号化します。  

Azure Key Vault は、シークレットの contentType フィールドもサポートしています。 クライアントは、シークレットのコンテンツ タイプ "contentType" を指定して、取得されるときのシークレット データの解釈を支援できます。 このフィールドの最大長は 255 文字です。 定義済みの値はありません。 推奨される使用方法は、シークレット データを解釈するためのヒントです。 たとえば、実装がパスワードと証明書の両方をシークレットとして格納する場合は、このフィールドを使ってどちらであるかを示します。 定義済みの値はありません。  

###  <a name="BKMK_SecretAttrs"></a> シークレットの属性

シークレット データに加えて、次の属性を指定できます。  

- *exp*: IntDate、省略可能、既定値は**無期限**。 *exp* (expiration time: 有効期限) 属性は、[特定の条件](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops)の場合を除き、それを過ぎたらシークレット データを取得してはならない日時を示します。 *exp* 属性の処理には、現在の日時が、*exp* 属性で指定されている有効期限日時以前でなければなりません。 Azure Key Vault では、クロックのスキューを考慮して、短い猶予 (通常は数分以下) を提供できます。 その値は、IntDate 値を含む数値でなければなりません。  
- *nbf*: IntDate、省略可能、既定値は**現在**。 *nbf* (not before: 有効期間開始日時) 属性は、[特定の条件](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops)の場合を除き、それより前はシークレット データを取得してはならない日時を示します。 *nbf* 属性の処理には、現在の日時が、*nbf* 属性で指定されている開始日時以降でなければなりません。 Azure Key Vault では、クロックのスキューを考慮して、短い猶予 (通常は数分以下) を提供できます。 その値は、IntDate 値を含む数値でなければなりません。  
- *enabled*: boolean、省略可能、既定値は **true**。 この属性は、シークレット データを取得できるかどうかを指定します。 enabled 属性は、nbf および *exp* と組み合わせて使います。nbf と exp の間で操作が発生する場合、enabled が **true** に設定されている場合にのみ許可されます。 *nbf* から *exp* までのウィンドウの外部での操作は、[特定の条件](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops)下を除き、自動的に禁止されます。  

シークレット属性を含むすべての応答に含まれる追加の読み取り専用属性があります。  

- *created*: IntDate、省略可能。 created 属性は、このバージョンのシークレットが作成された日時を示します。 この属性が追加される前に作成されたシークレットについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。  
- *updated*: IntDate、省略可能。 updated 属性は、このバージョンのシークレットが更新された日時を示します。 この属性が追加される前に最後に更新されたシークレットについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> 日付と時刻で制御される操作

シークレットの**取得**操作は、*nbf* / *exp* ウィンドウの外側の、有効期間前および期限切れ後のシークレットでも動作します。 有効期間前のシークレットの**取得**操作は、テスト目的に使用できます。 期限切れのシークレットの**取得**は、復旧操作に使用できます。

データ型について詳しくは、「[データ型](about-keys-secrets-and-certificates.md#BKMK_DataTypes)」をご覧ください。  

###  <a name="BKMK_SecretAccessControl"></a> シークレットのアクセス制御

Key Vault で管理されているシークレットのアクセス制御は、シークレットのコンテナーとして機能する Key Vault のレベルで提供されます。 シークレットのアクセス制御ポリシーは、同じキー コンテナー内のキーに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成してシークレットを保持することができ、シークレットのセグメント化と管理に適切なシナリオを維持する必要があります。 シークレットのアクセス制御は、キーのアクセス制御に依存しません。  

次のアクセス許可は、コンテナーのシークレット アクセス制御エントリでプリンシパルごとに使用でき、シークレット オブジェクトに対して許可される操作を厳密に反映します。  

-   *set*: 新しいシークレットを作成します  
-   *get*: シークレットを読み取ります  
-   *list*: キー コンテナーに格納されているシークレットまたはシークレットのバージョンを一覧表示します  
-   *delete*: シークレットを削除します  
-   *all*: すべてのアクセス許可  

シークレットの処理について詳しくは、[シークレットの操作](/rest/api/keyvault/secret-operations)に関するページをご覧ください。  

###  <a name="BKMK_SecretTags"></a> シークレットのタグ  
タグの形式で、アプリケーション固有の追加メタデータを指定できます。 Azure Key Vault は最大 15 個のタグをサポートし、それぞれが 256 文字の名前と 256 文字の値を持つことができます。  

>[!Note]
>呼び出し元は、そのオブジェクトの種類、キー、シークレット、または証明書に対して *list* または *get* アクセス許可を持っている場合、タグを読み取ることができます。

## <a name="key-vault-certificates"></a>Key Vault 証明書

Key Vault 証明書のサポートにより、x509 証明書が管理されるようになります。動作は次のとおりです。  

-   証明書の所有者が、Key Vault 作成プロセスを介して、または既存の証明書をインポートして、証明書を作成できます。 これには、自己署名証明書と証明機関によって生成された証明書の両方が含まれます。
-   Key Vault 証明書の所有者は、秘密キー マテリアルを操作せずに、X509 証明書のセキュリティ保護されたストレージと管理を実装できます。  
-   証明書所有者が、証明書のライフサイクルを管理するように Key Vault が指示するポリシーを作成できます。  
-   証明書所有者が、証明書の失効や更新のライフサイクル イベントを通知するために証明書の連絡先情報を指定できます。  
-   選択した発行者 (Key Vault パートナー X509 証明書プロバイダーまたは証明機関) による自動更新をサポートします。

>[!Note]
>パートナーではないプロバイダー/証明機関も許可されますが、自動更新機能はサポートされません。

###  <a name="BKMK_CompositionOfCertificate"></a> 証明書の構成

Key Vault 証明書が作成されると、アドレス指定可能なキーとシークレットも同じ名前で作成されます。 Key Vault のキーを使うとキー操作を行うことができ、Key Vault のシークレットを使うとシークレットとして証明書の値を取得できます。 Key Vault 証明書には、公開 x509 証明書メタデータも含まれます。  

証明書の ID とバージョンは、キーとシークレットに似ています。 Key Vault 証明書のバージョンで作成されるアドレス指定可能なキーとシークレットの特定のバージョンは、Key Vault 証明書の応答で使用できます。
 
![証明書は複合オブジェクトである](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> エクスポート可能なキーまたはエクスポート不可能なキー

証明書の作成に使用されたポリシーでキーがエクスポート可能であることが指定されている場合、作成された Key Vault 証明書は、秘密キーと共にアドレス指定可能なシークレットから PFX または PEM 形式で取得できます。 Key Vault 証明書の作成に使われたポリシーでキーがエクスポート不可能であることが示されていた場合は、シークレットとして取得される値に秘密キーは含まれません。  

アドレス指定可能なキーは、エクスポート不可能な KV 証明書との関連性が高くなります。 アドレス指定可能な KV キーの操作は、KV 証明書の作成に使用された KV 証明書ポリシーの *keyusage* フィールドからマップされます。  

証明書では、*RSA* または *RSA HSM* の 2 種類のキーがサポートされています。 エクスポート可能は RSA でのみ許可され、RSA HSM ではサポートされていません。  

###  <a name="BKMK_CertificateAttributesAndTags"></a> 証明書の属性とタグ

証明書のメタデータ、アドレス指定可能なキー、およびアドレス指定可能なシークレットだけでなく、Key Vault 証明書にも属性とタグが含まれます。  

#### <a name="attributes"></a>属性

証明書の属性は、KV 証明書の作成時に作成されるアドレス指定可能なキーとシークレットの属性に反映されます。  

Key Vault 証明書には次の属性があります。  

-   *enabled*: boolean、省略可能、既定値は **true**。 この属性を指定して、証明書のデータをシークレットとして取得できるか、またはキーとして操作可能かを示すことができます。 この属性は、*nbf* および *exp* と組み合わせて使います。*nbf* と *exp* の間で操作が発生する場合、enabled が true に設定されている場合にのみ許可されます。 *nbf* から *exp* までのウィンドウの外側の操作は、自動的に禁止されます。  

応答に含まれる追加の読み取り専用属性があります。

-   *created*: IntDate: このバージョンの証明書が作成された日時を示します。  
-   *updated*: IntDate: このバージョンの証明書が更新された日時を示します。  
-   *exp*: IntDate: x509 証明書の有効期限日の値を含みます。  
-   *nbf*: IntDate: x509 証明書の有効期間開始日の値を含みます。  

> [!Note] 
> Key Vault 証明書の期限が切れると、アドレス指定可能なキーとシークレットは機能しなくなります。  

#### <a name="tags"></a>タグ

 キーとシークレットのタグに似た、クライアントによって指定されたキーと値のペアのディクショナリです。  

 > [!Note]
> 呼び出し元は、そのオブジェクトの種類、キー、シークレット、または証明書に対して *list* または *get* アクセス許可を持っている場合、タグを読み取ることができます。

###  <a name="BKMK_CertificatePolicy"></a> 証明書ポリシー

証明書ポリシーには、KV 証明書のライフサイクルを作成して管理する方法に関する情報が含まれています。 秘密キーを含む証明書がキー コンテナーにインポートされると、X509 証明書を読み取ることによって既定のポリシーが作成されます。  

KV 証明書を最初から作成するときは、ポリシーを Key Vault に提供して、この KV 証明書バージョンまたは次の KV 証明書バージョンを作成する方法を指示する必要があります。 ポリシーが確立された後は、連続する作成操作で次の KV 証明書バージョンを作成する必要はありません。  

KV 証明書のすべてのバージョンに対して、ポリシーのインスタンスは 1 つだけ存在します。  

大まかに言えば、証明書ポリシーには次のものが含まれています。  

-   X509 証明書のプロパティ: サブジェクト名、サブジェクト代替名などを含みます。 x509 証明書要求の作成に使用されます。  
-   キーのプロパティ: キーの種類、キーの長さ、エクスポート可能、キー再利用のフィールドが含まれています。 これらのフィールドは、キー コンテナーにキーの生成方法を示します。  
-   シークレットのプロパティ: 証明書をシークレットとして取得するための、シークレット値を生成するアドレス指定可能なシークレットのコンテンツの種類などの、シークレットのプロパティが含まれます。  
-   有効期間アクション: KV 証明書の有効期間アクションが含まれます。 各有効期間アクションには次のものが含まれています。  

     - トリガー: 有効期限までの日数または有効期間のパーセンテージで指定されます  

     - アクション: アクションの種類 (*emailContacts* または *autoRenew*) を指定します  

-   発行者: x509 証明書の発行に使用する証明書発行者に関するパラメーターです。  
-   ポリシー属性: ポリシーに関連付けられた属性が含まれます  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 と Key Vault の使用方法の対応

次の表では、x509 キー使用ポリシーと、Key Vault 証明書作成の一部として作成されるキーの有効なキー操作との対応を示します。

|**X509 キー使用フラグ**|**Key Vault キー操作**|**既定の動作**|
|----------|--------|--------|
|DataEncipherment|encrypt、decrypt| 該当なし |
|DecipherOnly|復号化| 該当なし  |
|DigitalSignature|sign、verify| 証明書作成時に使用方法が指定されてない場合の Key Vault の既定値 | 
|EncipherOnly|encrypt| 該当なし |
|KeyCertSign|sign、verify|該当なし|
|KeyEncipherment|wrapKey、unwrapKey| 証明書作成時に使用方法が指定されてない場合の Key Vault の既定値 | 
|NonRepudiation|sign、verify| 該当なし |
|crlsign|sign、verify| 該当なし |

###  <a name="BKMK_CertificateIssuer"></a> 証明書の発行者

Key Vault 証明書オブジェクトは、x509 証明書を要求するための選択された証明書発行者プロバイダーとの通信に使用される構成を保持しています。  

-   Key Vault は、SSL 証明書の次の証明書発行者プロバイダーと提携しています

|**プロバイダー名**|**場所**|
|----------|--------|
|DigiCert|パブリック クラウドおよび Azure Government 内のすべての Key Vault サービスの場所でサポートされます|
|GlobalSign|パブリック クラウドおよび Azure Government 内のすべての Key Vault サービスの場所でサポートされます|

Key Vault で証明書発行者を作成するには、その前に、次の前提条件手順 1 と 2 を正常に完了する必要があります。  

1. 証明機関 (CA) プロバイダーにオンボードします  

    -   組織の管理者は、会社 (例:  Contoso) を少なくとも 1 つの CA プロバイダーにオンボードする必要があります。  

2. 管理者は、SSL 証明書を登録 (および更新) するための Key Vault に対する要求者資格情報を作成します  

    -   キー コンテナー内のプロバイダーの発行者オブジェクトの作成に使用される構成を提供します  

証明書ポータルから発行者オブジェクトを作成する方法について詳しくは、[Key Vault 証明書のブログ](http://aka.ms/kvcertsblog)をご覧ください  

Key Vault では、異なる発行者プロバイダー構成で複数の発行者オブジェクトを作成できます。 発行者オブジェクトが作成されたら、その名前を 1 つまたは複数の証明書ポリシーで参照できます。 発行者オブジェクトを参照することで、証明書の作成と更新中に CA プロバイダーに X509 証明書を要求するときに、発行者オブジェクトで指定されている構成を使用するよう Key Vault に指示します。  

発行者オブジェクトはコンテナー内に作成され、同じコンテナー内の KV 証明書でのみ使用できます。  

###  <a name="BKMK_CertificateContacts"></a> 証明書の連絡先

証明書の連絡先には、証明書有効期間イベントによってトリガーされる通知を送信する連絡先情報が含まれています。 連絡先情報は、キー コンテナー内のすべての証明書によって共有されます。 通知は、キー コンテナー内の任意の証明書のイベントに指定されているすべての連絡先に送信されます。  

証明書のポリシーが自動更新に設定されている場合は、次のイベントで通知が送信されます。  

-   証明書更新の前
-   証明書更新の後。証明書が正常に更新されたかどうか、またはエラーが発生して証明書の手動更新が必要かどうかを示します。  

 証明書のポリシーが手動更新に設定されている場合 (メールのみ)、証明書を更新する必要があるときに通知が送信されます。  

###  <a name="BKMK_CertificateAccessControl"></a> 証明書のアクセス制御

 証明書のアクセス制御は Key Vault によって管理され、それらの証明書のコンテナーとして機能する Key Vault のレベルで提供されます。 証明書のアクセス制御ポリシーは、同じキー コンテナー内のキーとシークレットに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成して証明書を保持することができ、証明書のセグメント化と管理に適切なシナリオを維持する必要があります。  

 次のアクセス許可は、キー コンテナーのシークレット アクセス制御エントリでプリンシパルごとに使用でき、シークレット オブジェクトに対して許可される操作を厳密に反映します。  

-   *get*: 証明書の現在のバージョンまたは証明書の任意のバージョンの取得を許可します 
-   *list*: 現在の証明書または証明書のバージョンの一覧表示を許可します  
-   *delete*: 証明書、そのポリシー、およびそのすべてのバージョンの削除を許可します  
-   *create*: Key Vault 証明書の作成を許可します。  
-   *import*: Key Vault 証明書への証明書マテリアルのインポートを許可します。  
-   *update*: 証明書の更新を許可します。  
-   *manageconnects*: Key Vault 証明書の連絡先の管理を許可します  
-   *getissuers*: 証明書の発行者の取得を許可します  
-   *listissuers*: 証明書の発行者の一覧表示を許可します  
-   *setissuers*: Key Vault 証明書発行者の作成または更新を許可します  
-   *deleteissuers*: Key Vault 証明書発行者の削除を許可します  
-   *all*: すべてのアクセス許可を付与します  

## <a name="additional-information-for-certificates"></a>証明書の追加情報

- [証明書とポリシー](/rest/api/keyvault/certificates-and-policies)
- [証明書の発行者](/rest/api/keyvault/certificate-issuers)
- [証明書の連絡先](/rest/api/keyvault/certificate-contacts)

## <a name="see-also"></a>関連項目

- [認証、要求、応答](authentication-requests-and-responses.md)
- [Key Vault のバージョン](key-vault-versions.md)
- [Key Vault 開発者ガイド](/azure/key-vault/key-vault-developers-guide)
