---
title: "SAS を利用した Azure CDN の使用 | Microsoft Docs"
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>SAS を利用した Azure CDN の使用

ストレージ アカウントのストレージ コンテナーからコンテンツを提供する場合は、ストレージ コンテナーへのプライベート アクセスを許可することで、ユーザーがファイルにアクセスする方法をセキュリティで保護できます。 そうしない場合、パブリック アクセスが許可されているストレージ コンテナーには、その URL を知っていればだれでもアクセスできます。 コンテンツ配信ネットワーク (CDN) へのアクセスを許可したストレージ アカウントを保護するには、Azure Storage からの Shared Access Signature (SAS) 機能を使用して、プライベート ストレージ コンテナーへの制限付きアクセスを許可できます。

SAS は、アカウント キーを公開することなく Azure Storage リソースへの制限付きアクセス権を付与する URI です。 ストレージ アカウント キーで信頼していないが特定のストレージ アカウント リソースへのアクセスを委任するクライアントに SAS を提供できます。 これらのクライアントに共有アクセス署名 URI を配布して、指定した期間にわたってリソースへのアクセスを許可します。
 
SAS では、開始時刻と有効期限、アクセス許可 (読み取り/書き込み)、IP 範囲など、BLOB へのアクセスに関するさまざまなパラメーターを定義することができます。 この記事では、Azure CDN と共に SAS を使用する方法について説明します。 作成方法やパラメーター オプションなど、SAS について詳しくは、「[Shared Access Signatures (SAS) の使用](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)」をご覧ください。

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>ストレージ SAS と共に使用するための Azure CDN の設定
SAS を Azure CDN と共に使用する場合は、次の 3 つのオプションをお勧めします。 すべてのオプションで、動作する SAS が既に作成されていることを前提としています (前提条件をご覧ください)。 
 
### <a name="prerequisites"></a>前提条件
まず、ストレージ アカウントを作成し、資産の SAS を生成します。 保存されている 2 種類のアクセス署名 (サービス SAS またはアカウント SAS) を生成できます。 詳しくは、「[共有アクセス署名の種類](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)」をご覧ください。

SAS を生成した後、次の形式の URL を使用して、Blob Storage ファイルにアクセスできます: `https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
例: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

パラメーターの設定について詳しくは、「[SAS parameter considerations](#sas-parameter-considerations)」(SAS パラメーターの考慮事項) と「[Shared Access Signature パラメーター](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters)」をご覧ください。

![CDN SAS 設定](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>オプション 1: CDN から Blob Storage へのパススルーと共に SAS を使用する

このオプションは最も簡単で、CDN から配信元サーバーに渡される 1 つの SAS トークンのみを使用します。 **Azure CDN from Verizon** の Standard と Premium の両方のプロファイルと **Azure CDN from Akamai** でサポートされています。 
 
1. エンドポイントを選択し、**[キャッシュ規則]** をクリックしてから、**[クエリ文字列のキャッシュ]** の一覧で **[一意の URL をすべてキャッシュ]** を選択します。

    ![CDN キャッシュ規則](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. ストレージ アカウントに SAS を設定したら、CDN の URL と共に SAS トークンを使用してファイルにアクセスします。 
   
   結果として生成される URL の形式は次のとおりです: `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   例:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. キャッシュ規則を使用するか、配信元に `Cache-Control` ヘッダーを追加して、キャッシュ期間を微調整します。 CDN では SAS トークンがプレーンなクエリ文字列として扱われるため、ベスト プラクティスとして、SAS の有効期限かそれより前に期限が切れるようにキャッシュ期間を設定する必要があります。 そうしないと、SAS がアクティブになっている期間よりも長くファイルがキャッシュされた場合、SAS の有効期限が経過した後も CDN 配信元サーバーからファイルにアクセスできます。 この状況が発生した場合、キャッシュされたファイルをアクセス不能にするには、ファイルに対して消去操作を実行して、キャッシュからクリアする必要があります。 CDN のキャッシュ期間の設定について詳しくは、「[Azure Content Delivery Network のキャッシュ動作をキャッシュ規則で制御する](cdn-caching-rules.md)」ご覧ください。

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>オプション 2: 書き換えルールを使用して CDN セキュリティ トークンを非表示にする
 
このオプションを使用して、CDN ユーザーに SAS トークンを要求せずに配信元の Blob Storage をセキュリティで保護できます。 ファイルに対する特定のアクセス制限が不要でも、CDN オフロード時間を短縮するためにユーザーがストレージ配信元に直接アクセスできないようにする場合は、このオプションを使用できます。 このオプションは、**Azure CDN Premium from Verizon** プロファイルでのみ使用できます。 
 
1. [ルール エンジン](cdn-rules-engine.md)を使用して URL 書き換えルールを作成します。 新しいルールは、反映されるまで約 90 分かかります。

   ![CDN の [管理] ボタン](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN の [ルール エンジン] ボタン](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   このサンプル URL 書き換えルールには次のパターンがあります。
   
   ソース:   
   `/test/demo.jpg`
   
   変換先:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 書き換えルール](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. 次の形式で、SAS トークンがない CDN のファイルにアクセスします: `https://<endpoint>.azureedge.net/<folder>/<file>`
 
   例:    
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   SAS トークンを使用するかどうかに関係なく、CDN エンドポイントにはだれでもアクセスできます。 

3. キャッシュ規則を使用するか、配信元に `Cache-Control` ヘッダーを追加して、キャッシュ期間を微調整します。 CDN では SAS トークンがプレーンなクエリ文字列として扱われるため、ベスト プラクティスとして、SAS の有効期限かそれより前に期限が切れるようにキャッシュ期間を設定する必要があります。 そうしないと、SAS がアクティブになっている期間よりも長くファイルがキャッシュされた場合、SAS の有効期限が経過した後も CDN 配信元サーバーからファイルにアクセスできます。 この状況が発生した場合、キャッシュされたファイルをアクセス不能にするには、ファイルに対して消去操作を実行して、キャッシュからクリアする必要があります。 CDN のキャッシュ期間の設定について詳しくは、「[Azure Content Delivery Network のキャッシュ動作をキャッシュ規則で制御する](cdn-caching-rules.md)」ご覧ください。

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>オプション 3: 書き換えルールと共に CDN セキュリティ トークン認証を使用する

このオプションは、最も安全かつカスタマイズ可能です。 CDN セキュリティ トークン認証を使用するには、**Azure CDN Premium from Verizon** プロファイルが必要です。 クライアント アクセスは、CDN セキュリティ トークンに設定されているセキュリティ パラメーターに基づきます。 ただし、SAS が無効になると、CDN は配信元サーバーからのコンテンツを再検証できなくなります。

1. [CDN セキュリティ トークンを作成](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication)し、CDN エンドポイントのルール エンジンとユーザーがファイルにアクセスできる場所のパスを使用してアクティブにします。

   SAS URL には次の形式があります。   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   例:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   CDN セキュリティ トークン認証のパラメーター オプションは、SAS トークンのパラメーター オプションとは異なります。 CDN セキュリティ トークンの作成時に有効期限を使用する場合は、SAS トークンの有効期限と同じ値に設定します。 これにより、有効期限が予測可能になります。 
 
2. [ルール エンジン](cdn-rules-engine.md)を使用して、コンテナー内のすべての BLOB へのトークン アクセスを有効にする URL 書き換えルールを作成します。 新しいルールは、反映されるまで約 90 分かかります。

   このサンプル URL 書き換えルールには次のパターンがあります。
   
   ソース:   
   `/test/demo.jpg`
   
   変換先:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 書き換えルール](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. SAS を更新する場合は、新しい SAS トークンを使用するように URL 書き換えルールを更新します。 

## <a name="sas-parameter-considerations"></a>SAS パラメーターに関する考慮事項

SAS パラメーターは CDN から参照できないため、CDN はそれらに基づいて配信動作を変更できません。 定義されているパラメーター制限は、CDN が配信元サーバーに対して行う要求にのみ適用され、クライアントから CDN への要求には適用されません。 この違いは、SAS パラメーターを設定するときに考慮する必要があります。 これらの高度な機能が必要で、[オプション 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule) を使用する場合は、CDN セキュリティ トークンに適切な制限を設定します。

| SAS パラメーター名 | [説明] |
| --- | --- |
| 開始 | CDN が BLOB ファイルへのアクセスを開始できる時刻。 クロック スキュー (クロックの信号が異なるコンポーネントに対して異なる時間に到着する) により、資産をすぐに使用可能にするには 15 分早い時間を選択します。 |
| End | CDN が BLOB ファイルにアクセスできなくなる時間。 以前に CDN にキャッシュされたファイルには引き続きアクセスできます。 ファイルの有効期限を制御するには、CDN セキュリティ トークンに適切な有効期限を設定するか、資産を消去します。 |
| 許可された IP アドレス | 省略可能。 **Azure CDN from Verizon** を使用する場合は、このパラメーターを「[Azure CDN from Verizon Edge Server IP Ranges](https://msdn.microsoft.com/library/mt757330.aspx)」(Azure CDN from Verizon Edge Server の IP 範囲) で定義されている範囲に設定できます。 **Azure CDN from Akamai** を使用する場合、IP アドレスは静的でないため、IP 範囲パラメーターを設定できません。|
| 許可されるプロトコル | アカウント SAS を使用して行われた要求に対して許可されるプロトコル。 HTTPS 設定をお勧めします。|

## <a name="see-also"></a>関連項目
- [Shared Access Signatures (SAS) の使用](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Shared Access Signature、第 2 部: BLOB ストレージでの SAS の作成と使用](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [トークン認証による Azure Content Delivery Network 資産の保護](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
