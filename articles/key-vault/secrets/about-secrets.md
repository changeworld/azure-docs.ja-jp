---
title: Azure Key Vault のシークレットについて - Azure Key Vault
description: シークレットに関する Azure Key Vault の REST インターフェイスと開発者の詳細の概要です。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2578f48ce218a0feaa5fb515ebc5d0e7154802ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420386"
---
# <a name="about-azure-key-vault-secrets"></a>Azure Key Vault のシークレットについて

Azure Key Vault では、Microsoft Azure アプリケーションとユーザーはいくつかの種類のシークレット データを保存して使用できます。

- シークレット:パスワードやデータベース接続文字列などのシークレットのセキュリティで保護されたストレージを提供します。

- Azure Storage:Azure Storage アカウントのキーを自動的に管理できます。 内部的には、Key Vault は Azure ストレージ アカウントのキーを一覧表示し (同期)、定期的にキーを再生成 (ローテーション) できます。 

Key Vault の一般的な情報については、「[Azure Key Vault とは](/azure/key-vault/key-vault-overview)」をご覧ください。

## <a name="azure-key-vault"></a>Azure Key Vault

以下のセクションでは、Key Vault サービスの実装に該当する一般的な情報を提供します。 

### <a name="objects-identifiers-and-versioning"></a>オブジェクト、識別子、バージョン管理

Key Vault に格納されるオブジェクトは、オブジェクトの新しいインスタンスが作成されるたびにバージョン管理されます。 各バージョンには、一意の識別子と URL が割り当てられます。 オブジェクトが最初に作成されるときに、オブジェクトに一意のバージョン識別子が指定され、オブジェクトの現在のバージョンとしてマークされます。 同じオブジェクト名の新しいインスタンスが作成されると、新しいオブジェクトに一意のバージョン識別子が与えられ、現在のバージョンになります。  

Key Vault 内のオブジェクトは、現在の識別子またはバージョン固有の識別子を使用してアドレス指定できます。 たとえば、キーの名前を `MasterKey` とすると、現在の識別子を指定して操作を実行すると、システムは使用可能な最新のバージョンを使用します。 バージョン固有の識別子を指定して操作を実行すると、システムはオブジェクトの特定のバージョンを使用します。  

Key Vault 内のオブジェクトは、URL を使用して一意に識別されます。 地理的場所に関係なく、システム内の複数のオブジェクトが同じ URL を持つことはありません。 オブジェクトの完全な URL は、オブジェクト識別子と呼ばれます。 URL は、Key Vault を示すプレフィックス、オブジェクトの種類、ユーザー指定のオブジェクト名、およびオブジェクトのバージョンで構成されます。 オブジェクト名は大文字と小文字が区別されず、変更できません。 オブジェクトのバージョンを含まない識別子は、ベース識別子と呼ばれます。  

詳しくは、「[Authentication, requests, and responses](../general/authentication-requests-and-responses.md)」(認証、要求、応答) をご覧ください。

オブジェクト識別子の一般的な形式は次のとおりです。  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

各値の説明:  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault サービスでのキー コンテナーの名前。<br /><br /> キー コンテナーの名前はユーザーが選択し、グローバルに一意です。<br /><br /> Key Vault の名前は、0 ～ 9、a ～ z、A ～ Z、- のみを使った 3 ～ 24 文字の文字列である必要があります。|  
|`object-type`|オブジェクトの種類で、"keys" または "secrets" です。|  
|`object-name`|`object-name` は、ユーザーが指定する名前で、キー コンテナー内で一意である必要があります。 名前は、0 ～ 9、a ～ z、A ～ Z、- のみを使った 1 ～ 127 文字の文字列である必要があります。|  
|`object-version`|`object-version` はシステムが生成し、オブジェクトの一意のバージョンに対応するために必要に応じて使用される 32 文字の文字列識別子です。|  

## <a name="key-vault-secrets"></a>キー コンテナーのシークレット 

### <a name="working-with-secrets"></a>シークレットの操作

開発者から見ると、Key Vault API はシークレット値を文字列として受け取って返します。 Key Vault の内部では、シークレットはオクテット (8 ビット バイト) のシーケンスとして格納および管理され、最大サイズはそれぞれ 25k バイトです。 Key Vault サービスでは、シークレットのセマンティクスは提供されていません。 データを受け取り、暗号化し、格納して、シークレット識別子 ("id") を返すだけです。 後で識別子を使用して、シークレットを取得できます。  

機密性の高いデータについては、クライアントで追加のデータ保護レイヤーを検討する必要があります。 たとえば、Key Vault に保存する前に別の保護キーを使用してデータを暗号化します。  

Key Vault では、シークレットの contentType フィールドもサポートされています。 クライアントは、シークレットのコンテンツ タイプを指定して、取得されるときのシークレット データの解釈を支援できます。 このフィールドの最大長は 255 文字です。 定義済みの値はありません。 推奨される使用方法は、シークレット データを解釈するためのヒントです。 たとえば、実装がパスワードと証明書の両方をシークレットとして格納する場合は、このフィールドを使用して区別します。 定義済みの値はありません。  

### <a name="secret-attributes"></a>シークレットの属性

シークレット データに加えて、次の属性を指定できます。  

- *exp*:IntDate、省略可能、既定値は**無期限**。 *exp* (expiration time: 有効期限) 属性は、[特定の条件](#date-time-controlled-operations)の場合を除き、それを過ぎたらシークレット データを取得してはならない日時を示します。 このフィールドは、特定のシークレットで使用できないキー コンテナー サービスをユーザーに通知するための**情報提供**のみを目的としています。 その値は、IntDate 値を含む数値でなければなりません。   
- *nbf*:IntDate、省略可能、既定値は**現在**。 *nbf* (not before: 有効期間開始日時) 属性は、[特定の条件](#date-time-controlled-operations)の場合を除き、それより前はシークレット データを取得してはならない日時を示します。 このフィールドは**情報提供**のみを目的としています。 その値は、IntDate 値を含む数値でなければなりません。 
- *enabled*: boolean、省略可能、既定値は **true**。 この属性は、シークレット データを取得できるかどうかを指定します。 enabled 属性は、*nbf* および *exp* と組み合わせて使います。*nbf* と *exp* の間で操作が発生する場合、enabled が **true** に設定されている場合にのみ許可されます。 *nbf* から *exp* までのウィンドウの外部での操作は、[特定の条件](#date-time-controlled-operations)下を除き、自動的に禁止されます。  

シークレット属性を含むすべての応答に含まれる追加の読み取り専用属性があります。  

- *created*:IntDate、省略可能。 created 属性は、このバージョンのシークレットが作成された日時を示します。 この属性が追加される前に作成されたシークレットについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。  
- *updated*:IntDate、省略可能。 updated 属性は、このバージョンのシークレットが更新された日時を示します。 この属性が追加される前に最後に更新されたシークレットについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。

#### <a name="date-time-controlled-operations"></a>日付と時刻で制御される操作

シークレットの**取得**操作は、*nbf* / *exp* ウィンドウの外側の、有効期間前および期限切れ後のシークレットでも動作します。 有効期間前のシークレットの**取得**操作は、テスト目的に使用できます。 期限切れのシークレットの**取得**は、復旧操作に使用できます。

### <a name="secret-access-control"></a>シークレットのアクセス制御

Key Vault で管理されているシークレットのアクセス制御は、そのシークレットを格納している Key Vault のレベルで提供されます。 シークレットのアクセス制御ポリシーは、同じキー コンテナー内のキーに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成してシークレットを保持することができ、シークレットのセグメント化と管理に適切なシナリオを維持する必要があります。   

次のアクセス許可は、コンテナーのシークレット アクセス制御エントリでプリンシパルごとに使用でき、シークレット オブジェクトに対して許可される操作を厳密に反映します。  

- シークレット管理操作に対するアクセス許可
  - *get*:シークレットを読み取ります  
  - *list*:キー コンテナーに格納されているシークレットまたはシークレットのバージョンを一覧表示します  
  - *set*:シークレットの作成  
  - *delete*:シークレットを削除します  
  - *recover*:削除されたシークレットを復旧します
  - *backup*:キー コンテナー内のシークレットをバックアップします
  - *restore*:バックアップしたシークレットをキー コンテナーに復元します

- 特権操作に対するアクセス許可
  - *purge*:削除されたシークレットをパージ (完全に削除) します

シークレットの処理について詳しくは、[Key Vault REST API リファレンス内のシークレットの操作](/rest/api/keyvault)の説明をご覧ください。 アクセス許可の設定については、「[Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate)」(コンテナー - 作成または更新) および「[Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy)」(コンテナー -アクセス ポリシーの更新) をご覧ください。 

### <a name="secret-tags"></a>シークレットのタグ  
タグの形式で、アプリケーション固有の追加メタデータを指定できます。 Key Vault は最大 15 個のタグをサポートし、それぞれが 256 文字の名前と 256 文字の値を持つことができます。  

>[!Note]
>タグは、呼び出し元が *list* または *get* のアクセス許可を持っている場合に呼び出し元によって読み取ることができます。

## <a name="azure-storage-account-key-management"></a>Azure ストレージ アカウント キーの管理

Key Vault では、Azure ストレージ アカウント キーを管理できます。

- Key Vault の内部では、Azure ストレージ アカウントを使用してキーの一覧表示 (同期) ができます。 
- Key Vault は定期的にキーを再生成 (ローテーション) します。
- キーの値は、呼び出し元に応答で返されることはありません。
- Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーが管理されます。

詳細については、[Azure Key Vault のストレージ アカウント キー](../secrets/overview-storage-keys.md)に関する記事をご覧ください。

### <a name="storage-account-access-control"></a>ストレージ アカウントのアクセス制御

ユーザーまたはアプリケーション プリンシパルがマネージド ストレージ アカウントに対する操作を実行するのを承認するときは、次のアクセス許可を使用できます。  

- マネージド ストレージ アカウントと SaS 定義の操作に対するアクセス許可
  - *get*:ストレージ アカウントに関する情報を取得します 
  - *list*:キー コンテナーによって管理されているストレージ アカウントを一覧表示します
  - *update*:ストレージ アカウントを更新します
  - *delete*:ストレージ アカウントを削除する  
  - *recover*:削除されたストレージ アカウントを復旧します
  - *backup*:ストレージ アカウントをバックアップします
  - *restore*:バックアップしたストレージ アカウントをキー コンテナーに復元します
  - *set*:ストレージ アカウントを作成または更新します
  - *regeneratekey*:ストレージ アカウントの指定されたキー値を再生成します
  - *getsas*:ストレージ アカウントの SAS 定義に関する情報を取得します
  - *listsas*:ストレージ アカウントのストレージ SAS 定義を一覧表示します
  - *deletesas*:ストレージ アカウントから SAS 定義を削除します
  - *setsas*:ストレージ アカウントの新しい SAS 定義/属性を作成または更新します

- 特権操作に対するアクセス許可
  - *purge*:マネージド ストレージ アカウントをパージ (完全に削除) します

詳しくは、[Key Vault REST API リファレンス内のストレージ アカウントの操作](/rest/api/keyvault)に関するページをご覧ください。 アクセス許可の設定については、「[Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate)」(コンテナー - 作成または更新) および「[Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy)」(コンテナー -アクセス ポリシーの更新) をご覧ください。

## <a name="see-also"></a>参照

- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
