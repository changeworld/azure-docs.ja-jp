---
title: Azure Attestation の要求セット
description: Azure Attestation の要求セット。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517557"
---
# <a name="claim-sets"></a>要求セット

Microsoft Azure Attestation を使用したエンクレーブの構成証明の過程で生成される要求は、以下のカテゴリに分類できます。

- **入力要求**: 構成証明の証拠を解析した後に Microsoft Azure Attestation によって生成される要求。ポリシーの作成者がカスタム ポリシーに承認規則を定義する際に使用できます

- **出力要求**: Azure Attestation によって生成されて構成証明トークンに追加される要求。

- **プロパティ要求**: Azure Attestation によって出力として作成される要求。 これには、レポートのエンコード、レポートの有効期間など、構成証明トークンのプロパティを表すすべての要求が含まれます。

## <a name="incoming-claims"></a>入力要求 

### <a name="sgx-attestation"></a>SGX の構成証明

ポリシーの作成者が SGX 構成証明ポリシーの承認規則を定義する際に使用する要求。

- **x-ms-sgx-is-debuggable**: エンクレーブのデバッグが有効になっているかどうかを示すブール値。
  
  SGX エンクレーブは、デバッグが無効でも有効でも読み込むことができます。 エンクレーブでこのフラグが true に設定されている場合、エンクレーブ コードのデバッグ機能が有効になります。 これには、エンクレーブのメモリにアクセスする機能が含まれます。 そのため、このフラグは開発目的でのみ true に設定することをお勧めします。 運用環境で有効にした場合、SGX のセキュリティ保証は保持されません。
  
  Azure Attestation ユーザーは、構成証明ポリシーを使用して、SGX エンクレーブに対してデバッグが無効になっているかどうかを確認できます。 ポリシー規則が追加されると、悪意のあるユーザーがエンクレーブのコンテンツにアクセスできるようにデバッグ サポートを有効にすると、構成証明は失敗します。

- **x-ms-sgx-product-id**: SGX エンクレーブの製品 ID を示す整数値。

  製品 ID は、エンクレーブの作成者によって各エンクレーブに割り当てられます。 製品 ID を使用すると、エンクレーブの作成者は、同じ MRSIGNER を使用して署名されたエンクレーブをセグメント化できます。 構成証明ポリシーに検証規則を追加することにより、顧客は目的のエンクレーブを使用しているかどうかを確認できます。 エンクレーブの製品 ID がエンクレーブの作成者によって発行された値と一致しない場合、構成証明は失敗します。

- **x-ms-sgx-mrsigner**: SGX エンクレーブの作成者を識別する文字列値。

  MRSIGNER は、エンクレーブ バイナリの署名に使用される、エンクレーブの作成者の公開キーのハッシュです。 構成証明ポリシーを介して MRSIGNER を検証することにより、顧客は信頼されたバイナリがエンクレーブ内で実行されているかどうかを確認できます。 ポリシー要求がエンクレーブの作成者の MRSIGNER と一致しない場合は、エンクレーブ バイナリが信頼できるソースによって署名されていないことを意味し、構成証明は失敗します。
  
  エンクレーブの作成者がセキュリティ上の理由から MRSIGNER をローテーションすることを希望する場合は、バイナリが更新される前に Azure Attestation ポリシーを更新して、新しい MRSIGNER 値と古い値をサポートする必要があります。 そうしないと、承認チェックが失敗し、結果的に構成証明が失敗します。
  
  構成証明ポリシーは、次の形式を使用して更新する必要があります。 
 
  #### <a name="before-key-rotation"></a>キーのローテーション前
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>キーのローテーション中

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>キーのローテーション後

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave**: エンクレーブ メモリに読み込まれているコードとデータを識別する文字列値。 

  MRENCLAVE は、エンクレーブ バイナリの確認に使用できるエンクレーブ測定値の 1 つです。 これは、エンクレーブ内で実行されているコードのハッシュです。 測定値は、エンクレーブ バイナリ コードが変更されるたびに変わります。 構成証明ポリシーを介して MRENCLAVE を検証することにより、顧客は対象のバイナリがエンクレーブ内で実行されているかどうかを確認できます。 ただし、MRENCLAVE は既存のコードに小さな変更が加えられるたびに頻繁に変更されるようになっているため、構成証明ポリシー内で MRSIGNER 検証を使用して、エンクレーブ バイナリを確認することをお勧めします。

- **x-ms-sgx-svn**: SGX エンクレーブのセキュリティ バージョン番号を示す整数値。

  セキュリティ バージョン番号 (SVN) は、エンクレーブの作成者によって SGX エンクレーブの各バージョンに割り当てられます。 エンクレーブ コードでセキュリティの問題が検出された場合、脆弱性の修正後、エンクレーブの作成者によって SVN 値が増分されます。 セキュリティで保護されていないエンクレーブ コードと対話できないように、顧客は構成証明ポリシーに検証規則を追加できます。 エンクレーブ コードの SVN がエンクレーブの作成者によって推奨されているバージョンと一致しない場合、構成証明は失敗します。

以下の要求は非推奨と見なされてはいますが、完全にサポートされており、今後も引き続き追加されます。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>TPM の構成証明

ポリシーの作成者が TPM 構成証明ポリシーの承認規則を定義する際に使用する要求。

- **aikValidated**: 構成証明識別キー (AIK) の証明書が検証済みであるかどうかの情報を含むブール値
- **aikPubHash**: base64(SHA256(DER 形式の AIK 公開キー)) を含む文字列
- **tpmVersion**:  トラステッド プラットフォーム モジュール (TPM) のメジャー バージョンを含む整数値
- **secureBootEnabled**: セキュア ブートが有効であるかどうかを示すブール値
- **iommuEnabled**: IOMMU (Input-Output Memory Management Unit) が有効であるかどうかを示すブール値
- **bootDebuggingDisabled**: ブート デバッグが無効であるかどうかを示すブール値
- **notSafeMode**: Windows がセーフ モードで実行されていないかどうかを示すブール値
- **notWinPE**: Windows が WinPE モードで実行されていないかどうかを示すブール値
- **vbsEnabled**: VBS が有効であるかどうかを示すブール値
- **vbsReportPresent**: VBS エンクレーブ レポートが利用できるかどうかを示すブール値

### <a name="vbs-attestation"></a>VBS の構成証明

ポリシーの作成者は、TPM 構成証明ポリシーの要求に加え、以下の要求を使用して、VBS 構成証明ポリシーの承認規則を定義できます。

- **enclaveAuthorId**: エンクレーブの作成者 ID (エンクレーブのプライマリ モジュールの作成者識別子) を Base64Url でエンコードした値を含む文字列値
- **enclaveImageId**: エンクレーブのイメージ ID (エンクレーブのプライマリ モジュールのイメージ識別子) を Base64Url でエンコードした値を含む文字列値
- **enclaveOwnerId**: エンクレーブの所有者 ID (エンクレーブの所有者識別子) を Base64Url でエンコードした値を含む文字列値
- **enclaveFamilyId**: エンクレーブのファミリ ID を Base64Url でエンコードした値を含む文字列値。 エンクレーブのプライマリ モジュールのファミリ ID です
- **enclaveSvn**: エンクレーブのプライマリ モジュールのセキュリティ バージョン番号を含む整数値
- **enclavePlatformSvn**: エンクレーブをホストするプラットフォームのセキュリティ バージョン番号を含む整数値
- **enclaveFlags**: enclaveFlags 要求は、エンクレーブのランタイム ポリシーを表すフラグを含んだ整数値

## <a name="outgoing-claims"></a>出力要求 

### <a name="common-for-all-attestation-types"></a>すべての構成証明タイプで共通

Azure Attestation では、すべての構成証明タイプについて、構成証明トークンに以下の要求が追加されます。 

- **x-ms-ver**: JWT スキーマ バージョン ("1.0")
- **x-ms-attestation-type**: 構成証明のタイプを表す文字列値 
- **x-ms-policy-hash**: BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text))))) として計算される Azure Attestation 評価ポリシーのハッシュ
- **x-ms-policy-signer**: "jwk" メンバーを含む JSON オブジェクト。"jwk" メンバーは、そのポリシーに署名する際に使用したキーを表します。 これは、ユーザーが署名済みのポリシーをアップロードするときに適用されます。

以下の要求名は、[IETF JWT 仕様](https://tools.ietf.org/html/rfc7519)から用いられます。

- **"jti" (JWT ID) 要求** - JWT の一意識別子
- **"iss" (Issuer) 要求** - JWT を発行したプリンシパル 
- **"iat" (Issued At) 要求** - JWT が発行された時刻を指定します。 
- **"exp" (Expiration Time) 要求** - JWT の有効期限。これ以降は、その JWT を受け入れて処理することはできません
- **"nbf" (Not Before) 要求** - JWT が有効になる日時を示します。これ以前にその JWT を受け入れて処理することはできません 

以下の要求名は、[IETF EAT ドラフト仕様](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)から用いられます

- **"Nonce 要求" (nonce)** - クライアントから提供されるオプションの nonce 値を変換せずに直接コピーしたもの 

以下の要求は非推奨と見なされてはいますが、完全にサポートされており、今後も引き続き追加されます。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX の構成証明 

次の要求は、SGX 構成証明のサービスによって生成され、構成証明トークンに含まれます。

- **x-ms-sgx-is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **x-ms-sgx-product-id**: SGX エンクレーブの製品 ID 値 
- **x-ms-sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-svn**: クォートでエンコードされたセキュリティ バージョン番号 
- **x-ms-sgx-ehd**: エンクレーブ保持データ。BASE64URL(エンクレーブ保持データ) として書式設定されます。
- **x-ms-sgx-collateral**: 構成証明の実行に使用される付帯情報を表す JSON オブジェクト。 x-ms-sgx-collateral 要求の値は、次のキーと値のペアを含んだ入れ子になった JSON オブジェクトです。
    - **qeidcertshash**: 証明書を発行する Quoting Enclave (QE) ID の SHA256 値
    - **qeidcrlhash**: QE Identity の発行側の証明書の CRL 一覧の SHA256 値
    - **qeidhash**: QE Identity の付帯情報の SHA256 値
    - **quotehash**: 評価されたクォートの SHA256 値
    - **tcbinfocertshash**: TCB Info の発行側の証明書の SHA256 値
    - **tcbinfocrlhash**: TCB Info の発行側の証明書の CRL 一覧の SHA256 値
    - **tcbinfohash**: TCB Info の付帯情報の SHA256 値

以下の要求は非推奨と見なされてはいますが、完全にサポートされており、今後も引き続き追加されます。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-collateral

### <a name="tpm-and-vbs-attestation"></a>TPM および VBS の構成証明

- **cnf (確認)** : "cnf" 要求は、所有証明キーを識別するために使用されます。 RFC 7800 で定義された確認要求には、JSON Web Key (JWK) オブジェクト (RFC 7517) として表された構成証明済みエンクレーブ キーのパブリック部分が含まれます
- **rp_data (証明書利用者データ)** : 要求で指定された証明書利用者データ (存在する場合)。証明書利用者がレポートの鮮度を保証するために nonce として使用されます。 rp_data は、rp_data が存在する場合にのみ追加されます

## <a name="property-claims"></a>プロパティ要求

### <a name="tpm-and-vbs-attestation"></a>TPM および VBS の構成証明

- **report_validity_in_minutes**: トークンの有効期間を示す整数要求。
  - **既定値 (時間)** : 1 日 (分単位)。
  - **最大値 (時間)** : 1 年 (分単位)。
- **omit_x5c**: サービスの信頼性の証しとなる証明書を Azure Attestation が省略すべきかどうかを示すブール値要求。 true の場合、構成証明トークンに x5t が追加されます。 false (既定値) の場合、構成証明トークンに x5c が追加されます。

## <a name="next-steps"></a>次のステップ
- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
