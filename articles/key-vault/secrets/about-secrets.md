---
title: Azure Key Vault のシークレットについて - Azure Key Vault
description: Azure Key Vault のシークレットの概要。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 54ebf914b27c8cd91272e4b8e5c8834e5331cdf4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057425"
---
# <a name="about-azure-key-vault-secrets"></a>Azure Key Vault のシークレットについて

[Key Vault](../general/overview.md) は、パスワードやデータベース接続文字列などの汎用シークレットのセキュリティで保護されたストレージを提供します。

開発者から見ると、Key Vault API はシークレット値を文字列として受け取って返します。 Key Vault の内部では、シークレットはオクテット (8 ビット バイト) のシーケンスとして格納および管理され、最大サイズはそれぞれ 25k バイトです。 Key Vault サービスでは、シークレットのセマンティクスは提供されていません。 データを受け取り、暗号化し、格納して、シークレット識別子 ("id") を返すだけです。 後で識別子を使用して、シークレットを取得できます。  

機密性の高いデータについては、クライアントで追加のデータ保護レイヤーを検討する必要があります。 たとえば、Key Vault に保存する前に別の保護キーを使用してデータを暗号化します。  

Key Vault では、シークレットの contentType フィールドもサポートされています。 クライアントは、シークレットのコンテンツ タイプを指定して、取得されるときのシークレット データの解釈を支援できます。 このフィールドの最大長は 255 文字です。 推奨される使用方法は、シークレット データを解釈するためのヒントです。 たとえば、実装がパスワードと証明書の両方をシークレットとして格納する場合は、このフィールドを使用して区別します。 定義済みの値はありません。  

## <a name="encryption"></a>暗号化

Key Vault 内のシークレットはすべて、暗号化した状態で格納されます。 Key Vault では、暗号化キーの階層を使用して保存中のシークレットを暗号化します。その階層内のすべてのキーは、FIPS 140-2 に準拠しているモジュールによって保護されます。 この暗号化は透過的に行われ、ユーザーによる操作は必要ありません。 ユーザーによって追加されたシークレットは、Azure Key Vault サービスによって暗号化され、ユーザーが読み取るとき自動的に暗号化が解除されます。

キー階層の暗号化リーフ キーは、各キー コンテナーに固有です。 キー階層の暗号化ルート キーはセキュリティ ワールドに固有であり、その保護レベルはリージョンによって異なります。
- 中国: ルート キーは、FIPS 140-2 レベル 1 で検証されたモジュールによって保護されます。 
- その他のリージョン: ルート キーは、FIPS 140-2 レベル 2 以上で検証されたモジュールによって保護されます。 

## <a name="secret-attributes"></a>シークレットの属性

シークレット データに加えて、次の属性を指定できます。  

- *exp*:IntDate、省略可能、既定値は **無期限**。 *exp* (expiration time: 有効期限) 属性は、[特定の条件](#date-time-controlled-operations)の場合を除き、それを過ぎたらシークレット データを取得してはならない日時を示します。 このフィールドは、特定のシークレットで使用できないキー コンテナー サービスをユーザーに通知するための **情報提供** のみを目的としています。 その値は、IntDate 値を含む数値でなければなりません。   
- *nbf*:IntDate、省略可能、既定値は **現在**。 *nbf* (not before: 有効期間開始日時) 属性は、[特定の条件](#date-time-controlled-operations)の場合を除き、それより前はシークレット データを取得してはならない日時を示します。 このフィールドは **情報提供** のみを目的としています。 その値は、IntDate 値を含む数値でなければなりません。 
- *enabled*: boolean、省略可能、既定値は **true**。 この属性は、シークレット データを取得できるかどうかを指定します。 enabled 属性は、*nbf* および *exp* と組み合わせて使います。*nbf* と *exp* の間で操作が発生する場合、enabled が **true** に設定されている場合にのみ許可されます。 *nbf* から *exp* までのウィンドウの外部での操作は、[特定の条件](#date-time-controlled-operations)下を除き、自動的に禁止されます。  

シークレット属性を含むすべての応答に含まれる追加の読み取り専用属性があります。  

- *created*:IntDate、省略可能。 created 属性は、このバージョンのシークレットが作成された日時を示します。 この属性が追加される前に作成されたシークレットについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。  
- *updated*:IntDate、省略可能。 updated 属性は、このバージョンのシークレットが更新された日時を示します。 この属性が追加される前に最後に更新されたシークレットについては、この値は null です。 その値は、IntDate 値を含む数値でなければなりません。

各キー コンテナー オブジェクトの種類の一般的な属性については、「[Azure Key Vault のキー、シークレット、証明書の概要](../general/about-keys-secrets-certificates.md)」をご覧ください。

### <a name="date-time-controlled-operations"></a>日付と時刻で制御される操作

シークレットの **取得** 操作は、*nbf* / *exp* ウィンドウの外側の、有効期間前および期限切れ後のシークレットでも動作します。 有効期間前のシークレットの **取得** 操作は、テスト目的に使用できます。 期限切れのシークレットの **取得** は、復旧操作に使用できます。

## <a name="secret-access-control"></a>シークレットのアクセス制御

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

Key Vault でアクセスを制御するための攻略ガイドについては、次の記事をご覧ください。
- [CLI を使用して Key Vault アクセス ポリシーを割り当てる](../general/assign-access-policy-cli.md)
- [PowerShell を使用して Key Vault アクセス ポリシーを割り当てる](../general/assign-access-policy-powershell.md)
- [Azure portal を使用して Key Vault アクセス ポリシーを割り当てる](../general/assign-access-policy-portal.md)
- [Azure のロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する](../general/rbac-guide.md)

## <a name="secret-tags"></a>シークレットのタグ  
タグの形式で、アプリケーション固有の追加メタデータを指定できます。 Key Vault は最大 15 個のタグをサポートし、それぞれが 256 文字の名前と 256 文字の値を持つことができます。  

>[!Note]
>タグは、呼び出し元が *list* または *get* のアクセス許可を持っている場合に呼び出し元によって読み取ることができます。

## <a name="usage-scenarios"></a>使用シナリオ

| 使用する場合 | 使用例 |
|--------------|-------------|
|パスワード、アクセス キー、サービス プリンシパル クライアント シークレットなどのサービス間通信の資格情報を安全に格納し、ライフサイクルを管理し、監視します。  | - [仮想マシンで Azure Key Vault を使用する](../general/tutorial-net-virtual-machine.md)<br> - [Azure Web アプリで Azure Key Vault を使用する](../general/tutorial-net-create-vault-azure-web-app.md) |

## <a name="next-steps"></a>次のステップ

- [Key Vault でのシークレットの管理に関するベスト プラクティス](secrets-best-practices.md)
- [Key Vault について](../general/overview.md)
- [キー、シークレット、証明書について](../general/about-keys-secrets-certificates.md)
- [Key Vault アクセス ポリシーを割り当てる](../general/assign-access-policy.md)
- [Azure のロールベースのアクセス制御を使用して Key Vault のキー、証明書、シークレットへのアクセス権を付与する](../general/rbac-guide.md)
- [キー コンテナーへのアクセスをセキュリティで保護する](../general/security-features.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
