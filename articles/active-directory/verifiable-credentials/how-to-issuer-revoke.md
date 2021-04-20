---
title: 検証可能な資格情報を発行者として失効させる方法 - Azure Active Directory Verifiable Credentials
description: 発行済みの検証可能な資格情報を失効させる方法について説明します
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222845"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>以前に発行された検証可能な資格情報を失効させる (プレビュー)

検証可能な資格情報 (VC) を操作するプロセスの一環として、資格情報を発行するだけでなく、資格情報を失効させる必要がある場合もあります。 この記事では、VC 仕様の **状態** プロパティ部分について説明し、失効プロセス、資格情報を失効させる必要が生じる理由、データとプライバシーへのいくつかの影響について詳しく見ていきます。

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="status-property-in-verifiable-credentials-specification"></a>検証可能な資格情報の仕様における状態プロパティ

検証可能な資格情報を失効させた場合の影響を理解するには、**状態チェック** の内容とその現在の動作を把握しておくと役立つことがあります。

[W3C の検証可能な資格情報の仕様](https://www.w3.org/TR/vc-data-model/)に関するページの [セクション 4.9](https://www.w3.org/TR/vc-data-model/#status) で、**状態** プロパティについて言及されています。

「この仕様では、検証可能な資格情報の現在の状態に関する情報 (中断または失効しているかどうかなど) を検出するために、次の **credentialStatus** プロパティを定義します。」

ただし、W3C 仕様では **状態チェック** を実装する方法の形式が定義されていません。

「状態スキームのデータ モデル、形式、およびプロトコルの定義は、この仕様の範囲外です。 検証可能な資格情報の状態チェックを実装する必要がある実装者向けに、使用可能な状態スキームが含まれる検証可能な資格情報拡張レジストリ [VC-EXTENSION-REGISTRY] が存在します。」

>[!NOTE]
>現時点では、Microsoft の状態チェックの実装は独自仕様ですが、標準に合わせるように DID のコミュニティと積極的に連携しています。

## <a name="how-does-the-status-property-work"></a>**状態** プロパティが機能する方法

Microsoft が発行したすべての検証可能な資格情報には、credentialStatus という属性があります。 これには、Microsoft がお客様に代わって管理する状態 API が設定されています。 これは、たとえば以下のようなものです。

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

オープン ソースの検証可能な資格情報 SDK では、状態 API の呼び出しと必要なデータの提供が処理されます。

API が呼び出され、適切な情報が提供されると、API は True または False のいずれかを返します。 True の場合、検証可能な資格情報が発行者によって引き続きアクティブになっており、False の場合は、検証可能な資格情報が発行者によって自発的に失効させられたことを示します。

## <a name="why-you-may-want-to-revoke-a-vc"></a>VC を失効させることが必要な理由

顧客にはそれぞれ、検証可能な資格情報を失効させる必要があることに対して固有の理由がありますが、ここでは、これまでに聞いたことのある一般的なテーマをいくつか紹介します。 

- 学生 ID: 学生が、大学の現役の学生ではなくなった。
- 従業員 ID: 従業員が、現職の従業員ではなくなった。
- 州の運転免許: ドライバーが、もうその州に住んでいない。

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>失効の機能を持つ検証可能な資格情報を設定する方法

すべての検証可能な資格情報データは、既定では Microsoft によって保存されません。 そのため、特定の検証可能な資格情報 ID を失効させるために参照するデータは Microsoft にありません。 発行者は、Microsoft がインデックスを作成し、その後ソルト化とハッシュ化を行うために、検証可能な資格情報の属性から特定のフィールドを指定する必要があります。

>[!NOTE]
>ハッシュ化は一方向の暗号化操作であり、```preimage``` と呼ばれる入力を変換し、固定長を持つ「ハッシュ」と呼ばれる出力を生成します。 現時点では、ハッシュ操作を計算によって元に戻すことはできません。

インデックスを付ける検証可能な資格情報の属性を Microsoft に伝えることができます。 インデックスを付けると、インデックス値を使用して、失効させる VC の検証可能な資格情報を検索できます。

**例:** Alice は Woodgrove の従業員です。 Alice は Contoso で働くために Woodgrove を退社しました。 Woodgrove の IT 管理者である Jane は、検証可能な資格情報の失効検索クエリで Alice のメールを検索します。 この例では、Jane は Woodgrove の検証済み従業員資格情報の電子メール フィールドにインデックスを付けます。 

インデックスを含めるために規則ファイルを変更する方法については、下の例を参照してください。

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>1 つの規則ファイルからは、1 つの属性にのみインデックスを付けることができます。  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>検証可能な資格情報を失効させる方法

インデックス要求が設定され、検証可能な資格情報がユーザーに発行されたら、次に VC ブレードで検証可能な資格情報を失効させる方法を確認します。

1. Azure Active Directory の検証可能な資格情報ブレードに移動します。
1. 以前インデックス要求が設定され、検証可能な資格情報をユーザーに発行した、検証可能な資格情報を選択します。 =
1. 左側のメニューで、 **[資格情報の取り消し]** を選択します。
   ![ 資格情報の取り消し](media/how-to-issuer-revoke/settings-revoke.png) 
1. 失効させるユーザーの index 属性を検索します。 

   ![失効させる資格情報を検索する](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >検証可能な資格情報からインデックス付き要求のハッシュのみが格納されるため、完全一致のみが検索結果に設定されます。 IT 管理者によって検索された入力を取得し、同じハッシュ アルゴリズムを使用して、データベースにハッシュ一致があるかどうかを確認します。
    
1. 一致が見つかったら、失効させる資格情報の右側にある **[取り消す]** オプションを選択します。

   ![失効後にユーザーに資格情報が残っていることを知らせる警告](media/how-to-issuer-revoke/warning.png) 

1. 失効が正常に終了すると、状態が更新され、ページの上部に緑色のバナーが表示されます。 
   ![設定でこのドメインを確認する](media/how-to-issuer-revoke/revoke-successful.png) 

これで、証明書利用者がこの特定の検証可能な資格情報の状態を確認するために呼び出しを行った場合、テナントの代理として動作する Microsoft の 状態 API によって 'false' 応答が返されます。

## <a name="next-steps"></a>次の手順

フローに慣れるために、テスト資格情報を使用して自分で機能を試してみます。 [チュートリアルを参照する](get-started-verifiable-credentials.md)ことによって、検証可能な資格情報を発行するようにテナントを構成する方法を見つけることができます。