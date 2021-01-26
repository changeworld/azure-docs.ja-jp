---
title: Azure Attestation の要求セット
description: Azure Attestation の要求セット。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: afe2cf288cd4a15091e8278309b3ecf74a2d35a4
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572750"
---
# <a name="claim-sets"></a>要求セット

Microsoft Azure Attestation を使用したエンクレーブの構成証明の過程で生成される要求は、以下のカテゴリに分類できます。

- **入力要求**: 構成証明の証拠を解析した後に Microsoft Azure Attestation によって生成される要求。ポリシーの作成者がカスタム ポリシーに承認規則を定義する際に使用できます。

- **出力要求**: Azure Attestation によって生成される要求。最終的に構成証明トークンに追加される要求がすべて含まれます。

- **プロパティ要求**: Azure Attestation によって出力として作成される要求。 これには、レポートのエンコード、レポートの有効期間など、構成証明トークンのプロパティを表すすべての要求が含まれます。

### <a name="common-incoming-claims-across-all-attestation-types"></a>すべての構成証明タイプに共通する入力要求

次の要求は、Azure Attestation によって生成されます。カスタム ポリシーに承認規則を定義する際にこれらの要求を使用できます。
- **x-ms-ver**: JWT スキーマ バージョン ("1.0")
- **x-ms-attestation-type**: 構成証明のタイプを表す文字列値 
- **x-ms-policy-hash**: BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text))))) として計算される Azure Attestation 評価ポリシーのハッシュ
- **x-ms-policy-signer**: "jwk" メンバーを含む JSON オブジェクト。"jwk" メンバーは、ユーザーが署名済みのポリシーをアップロードするとき、そのポリシーに署名する際に使用したキーを表します。

以下の要求は非推奨と見なされてはいますが、完全にサポートされています。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
maa-policyHash | x-ms-policy-hash
policy_hash | x-ms-policy-hash
policy_signer | x-ms-policy-signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>すべての構成証明タイプに共通する出力要求

[IETF JWT](https://tools.ietf.org/html/rfc7519) によって定義され、かつ Azure Attestation によって応答オブジェクトの中で使用される要求は以下のとおりです。

- **"jti" (JWT ID) 要求**
- **"iss" (発行者) 要求**
- **"iat" (発行時刻) 要求**
- **"exp" (有効期限) 要求**
- **"nbf" (期間の開始時刻) 要求**

[IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) によって定義され、かつ Azure Attestation によって応答オブジェクトの中で使用される要求は以下のとおりです。
- **"Nonce 要求" (nonce)**

## <a name="claims-specific-to-sgx-enclaves"></a>SGX エンクレーブに固有の要求

### <a name="incoming-claims-specific-to-sgx-attestation"></a>SGX 構成証明に固有の入力要求

次の要求は、SGX 構成証明のサービスによって生成されます。カスタム ポリシーに承認規則を定義する際にこれらの要求を使用できます。
- **x-ms-sgx-is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-svn**: クォートでエンコードされたセキュリティ バージョン番号 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>SGX 構成証明に固有の出力要求

次の要求はサービスによって生成され、SGX 構成証明の応答オブジェクトに追加されます。
- **x-ms-sgx-is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **x-ms-sgx-product-id**
- **x-ms-ver**
- **x-ms-sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-svn**: クォートでエンコードされたセキュリティ バージョン番号 
- **x-ms-sgx-ehd**: エンクレーブ保持データ。BASE64URL(エンクレーブ保持データ) として書式設定されます。
- **x-ms-sgx-collateral**: 構成証明の実行に使用される付帯情報を表す JSON オブジェクト。 x-ms-sgx-collateral 要求の値は、次のキーと値のペアを含んだ入れ子になった JSON オブジェクトです。
    - **qeidcertshash**: QE Identity の発行側の証明書の SHA256 値
    - **qeidcrlhash**: QE Identity の発行側の証明書の CRL 一覧の SHA256 値
    - **qeidhash**: QE Identity の付帯情報の SHA256 値
    - **quotehash**: 評価されたクォートの SHA256 値
    - **tcbinfocertshash**: TCB Info の発行側の証明書の SHA256 値
    - **tcbinfocrlhash**: TCB Info の発行側の証明書の CRL 一覧の SHA256 値
    - **tcbinfohash**: 構成証明の実行に使用される付帯情報を表す JSON オブジェクト

以下の要求は非推奨と見なされてはいますが、完全にサポートされており、今後も引き続き追加されます。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product-id | x-ms-sgx-product-id
$svn | x-ms-sgx-svn
$tee | x-ms-attestation-type
maa-ehd | x-ms-sgx-ehd
aas-ehd | x-ms-sgx-ehd
maa-attestationcollateral | x-ms-sgx-collateral

## <a name="claims-issued-specific-to-trusted-platform-module-tpm-attestation"></a>トラステッド プラットフォーム モジュール (TPM) の構成証明でのみ発行される要求

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>入力要求 (出力要求として使用することも可能)

- **aikValidated**: 構成証明識別キー (AIK) の証明書が検証済みであるかどうかの情報を含むブール値。
- **aikPubHash**: base64(SHA256(DER 形式の AIK 公開キー)) を含む文字列。
- **tpmVersion**:  トラステッド プラットフォーム モジュール (TPM) のメジャー バージョンを含む整数値。
- **secureBootEnabled**: セキュア ブートが有効であるかどうかを示すブール値。
- **iommuEnabled**: IOMMU (Input-Output Memory Management Unit) が有効であるかどうかを示すブール値。
- **bootDebuggingDisabled**: ブート デバッグが無効であるかどうかを示すブール値。
- **notSafeMode**: Windows がセーフ モードで実行されていないかどうかを示すブール値。
- **notWinPE**: Windows が WinPE モードで実行されていないかどうかを示すブール値。
- **vbsEnabled**: VBS が有効であるかどうかを示すブール値。
- **vbsReportPresent**: VBS エンクレーブ レポートが利用できるかどうかを示すブール値。
- **enclaveAuthorId**: エンクレーブの作成者 ID (エンクレーブのプライマリ モジュールの作成者識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveImageId**: エンクレーブのイメージ ID (エンクレーブのプライマリ モジュールのイメージ識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveOwnerId**: エンクレーブの所有者 ID (エンクレーブの所有者識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveFamilyId**: エンクレーブのファミリ ID を Base64Url でエンコードした値を含む文字列値。 エンクレーブのプライマリ モジュールのファミリ ID です。
- **enclaveSvn**: エンクレーブのプライマリ モジュールのセキュリティ バージョン番号を含む整数値。
- **enclavePlatformSvn**: エンクレーブをホストするプラットフォームのセキュリティ バージョン番号を含む整数値。
- **enclaveFlags**: enclaveFlags 要求は、エンクレーブのランタイム ポリシーを表すフラグを含んだ整数値。
  
### <a name="outgoing-claims-specific-to-tpm-attestation"></a>TPM 構成証明に固有の出力要求

- **policy_hash**: BASE64URL によって計算されたポリシー テキストの SHA256 ハッシュを含む文字列値 (SHA256(BASE64URL(UTF8(ポリシー テキスト))))。
- **policy_signer**: 署名済みのポリシー ヘッダーに存在する証明書チェーンまたは公開キーを含む JWK が含まれます。
- **ver (バージョン)** : レポートのバージョンを含んだ文字列値。 現在は 1.0 です。
- **cnf (確認) 要求**: "cnf" 要求は、所有証明キーを識別するために使用されます。 RFC 7800 で定義された確認要求には、JSON Web Key (JWK) オブジェクト (RFC 7517) として表された構成証明済みエンクレーブ キーのパブリック部分が含まれます。
- **rp_data (証明書利用者データ)** : 要求で指定された証明書利用者データ (存在する場合)。証明書利用者がレポートの鮮度を保証するために nonce として使用されます。
- **"jti" (JWT ID) 要求**: "jti" (JWT ID) 要求は、JWT の一意の識別子を提供します。 この識別子の値は、同じ値が誤って異なるデータ オブジェクトに割り当てられる可能性が無視できるほど小さい方法で割り当てられます。

### <a name="property-claims"></a>プロパティ要求

- **report_validity_in_minutes**: トークンの有効期間を示す整数要求。
  - **既定値 (時間)** : 1 日 (分単位)。
  - **最大値 (時間)** : 1 年 (分単位)。
- **omit_x5c**: サービスの信頼性の証しとなる証明書を Azure Attestation が省略すべきかどうかを示すブール値要求。 true の場合、構成証明トークンに x5t が追加されます。 false (既定値) の場合、構成証明トークンに x5c が追加されます。

## <a name="next-steps"></a>次のステップ
- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
