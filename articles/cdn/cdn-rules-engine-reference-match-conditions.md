---
title: "Azure CDN ルール エンジンの一致条件 | Microsoft Docs"
description: "Azure Content Delivery Network ルール エンジンの一致条件に関するリファレンス ドキュメント"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Azure CDN ルール エンジンの一致条件
この記事では、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-rules-engine.md)で利用できる一致条件について詳しく説明します。

ルールの 2 番目の部分は、一致条件です。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して一連の機能が実行されます。

たとえば、以下のような場合に一致条件を使用できます。
- 特定の場所でコンテンツの要求をフィルター処理する。
- 特定の IP アドレスまたは国から生成された要求をフィルター処理する。
- ヘッダー情報別に要求をフィルター処理する。

## <a name="always-match-condition"></a>「Always (常に)」一致条件

「Always (常に)」一致条件では、一連の既定の機能がすべての要求に適用されます。

名前 | 目的
-----|--------
[Always (常に)](#always) | 一連の既定の機能をすべての要求に適用します。

## <a name="device-match-condition"></a>「Device (デバイス)」一致条件

この「Device (デバイス)」一致条件では、モバイル デバイスからの要求をそのプロパティに基づいて識別します。  

名前 | 目的
-----|--------
[デバイス](#device) | モバイル デバイスからの要求をそのプロパティに基づいて識別します。

## <a name="location-match-conditions"></a>「Location (場所)」一致条件

「Location (場所)」一致条件では、要求者の位置情報に基づいて要求を識別します。

名前 | 目的
-----|--------
[AS Number (AS 番号)](#as-number) | 特定のネットワークから送信される要求を識別します。
[Country (国)](#country) | 指定した国から送信される要求を識別します。

## <a name="origin-match-conditions"></a>「配信元 (Origin)」一致条件

「配信元 (Origin)」一致条件では、Content Delivery Network ストレージまたは顧客の配信元サーバーを参照する要求を識別します。

名前 | 目的
-----|--------
[CDN Origin (CDN 配信元)](#cdn-origin) | Content Delivery Network ストレージに格納されたコンテンツの要求を識別します。
[Customer Origin (顧客配信元)](#customer-origin) | 特定の顧客の配信元サーバーに格納されているコンテンツの要求を識別します。

## <a name="request-match-conditions"></a>「要求 (Request)」一致条件

「要求 (Request)」一致条件では、プロパティに基づいて要求が識別されます。

名前 | 目的
-----|--------
[Client IP Address (クライアント IP アドレス)](#client-ip-address) | 特定の IP アドレスから送信される要求を識別します。
[Cookie Parameter (Cookie パラメーター)](#cookie-parameter) | 指定した値の各要求に関連付けられている Cookie を確認します。
[Cookie Parameter Regex (Cookie パラメーターの正規表現)](#cookie-parameter-regex) | 指定した正規表現の各要求に関連付けられている Cookie を確認します。
[Edge Cname (エッジ CNAME)](#edge-cname) | 特定の CNAME を指す要求を識別します。
[Referring Domain (参照ドメイン)](#referring-domain) | 指定したホスト名から参照された要求を識別します。
[Request Header Literal (要求ヘッダーのリテラル)](#request-header-literal) | 指定値に設定されている指定ヘッダーが含まれる要求を識別します。
[Request Header Regex (要求ヘッダーの正規表現)](#request-header-regex) | 指定正規表現に一致する値に設定されている指定ヘッダーが含まれる要求を識別します。
[Request Header Wildcard (要求ヘッダーのワイルドカード)](#request-header-wildcard) | 指定パターンに一致する値に設定されている指定ヘッダーが含まれる要求を識別します。
[Request Method (要求メソッド)](#request-method) | HTTP メソッドで要求を識別します。
[Request Scheme (要求スキーム)](#request-scheme) | HTTP プロトコルで要求を識別します。

## <a name="url-match-conditions"></a>「URL」一致条件

「URL」一致条件では、URL に基づいて要求が識別されます。

名前 | 目的
-----|--------
URL Path Directory (URL パス ディレクトリ) | 相対パスで要求を識別します。
URL Path Extension (URL パス拡張子) | ファイル名の拡張子で要求を識別します。
URL Path Filename (URL パス ファイル名) | ファイル名で要求を識別します。
URL Path Literal (URL パス リテラル) | 要求の相対パスを指定値と比較します。
URL Path Regex (URL パス正規表現) | 要求の相対パスを指定正規表現と比較します。
URL Path Wildcard (URL パス ワイルドカード) | 要求の相対パスを指定パターンと比較します。
URL Query Literal (URL クエリ リテラル) | 要求のクエリ文字列を指定値と比較します。
URL Query Parameter (URL クエリ パラメーター) | 指定パターンに一致する値に設定されている指定クエリ文字列パラメーターが含まれる要求を識別します。
URL Query Regex (URL クエリ正規表現) | 指定正規表現に一致する値に設定されている指定クエリ文字列パラメーターが含まれる要求を識別します。
URL Query Wildcard (URL クエリ ワイルドカード) | 指定値を要求のクエリ文字列と比較します。


## <a name="reference-for-rules-engine-match-conditions"></a>ルール エンジンの一致条件のリファレンス

---
### <a name="always"></a>Always (常に)

「Always (常に)」一致条件では、一連の既定の機能がすべての要求に適用されます。

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>AS Number (AS 番号) 
AS Number ネットワークは、自律システム番号 (ASN) で識別されています。 クライアントのネットワークが指定された ASN と "一致する" または "一致しない" 場合に、この条件が満たされるかを指示するためのオプションが提供されています。

重要な情報: 
- 複数の ASN を指定する場合は、ASN ごとに単一の空白で区切ります。 たとえば、"64514 64515" と指定した場合、64514 または 64515 のいずれかから配信された要求と一致します。
- 特定の要求から有効な ASN を返されない場合があります。 疑問符 (?) は、有効な ASN を特定できなかった要求と一致します。
- 目的のネットワークに対して ASN 全体を指定する必要があります。 部分的な値を指定しても、一致しません。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>CDN Origin (CDN 配信元)
以下の両方の条件が満たされている場合に、「CDN Origin (CDN 配信元)」一致条件が満たされます。
- Content Delivery Network ストレージからコンテンツが要求された。
- 要求 URI は、この一致条件で定義されているコンテンツ アクセス ポイント (たとえば、/000001) を使用する。
  - Content Delivery Network URL: 要求 URI には、選択したコンテンツ アクセス ポイントを含める必要があります。
  - エッジ CNAME URL: 対応するエッジ CNAME 構成では、選択したコンテンツ アクセス ポイントを参照する必要があります。
  
重要な情報: 
 - コンテンツ アクセス ポイントは、要求されたコンテンツを提供する必要があるサービスを識別します。
 - AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「CDN Origin (CDN 配信元)」一致条件と「Customer Origin (顧客配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「CDN Origin (CDN 配信元)」一致条件を AND IF ステートメントで結合することはできません。

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Client IP Address (現在の IP アドレス)
クライアントの IP アドレスが指定された IP アドレスと "一致する" または "一致しない" 場合に、「Client IP Address (クライアント IP アドレス)」条件が満たされるかを指示すためのオプションが提供されています。

重要な情報: 
- 必ず CIDR 表記を使用してください。
- 複数の IP アドレスまたは IP アドレス ブロックを指定する場合は、IP アドレスまたは IP ブロックごとに単一の空白で区切ります。
  - **IPv4 の例**: "1.2.3.4 10.20.30.40" と指定した場合、1.2.3.4 または 10.20.30.40 のいずれかから配信される任意の要求と一致します。
  - **IPv6 の例**: "1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80" と指定した場合、1:2:3:4:5:6:7:8 または 10:20:30:40:50:60:70:80 のいずれかから配信される要求と一致します。
- IP アドレス ブロックの構文では、ベース IP アドレスの末尾にスラッシュおよびプレフィックス サイズを付与します。
  - **IPv4 の例**: "5.5.5.64/26" と指定した場合、5.5.5.64 ～ 5.5.5.127 から配信される任意の要求と一致します。
  - **IPv6 の例**: "1:2:3:/48" と指定した場合、1:2:3:0:0:0:0:0 ～ 1:2:3:ffff:ffff:ffff:ffff:ffff から配信される任意の要求と一致します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Cookie Parameter (Cookie パラメーター)
**[一致する]**/**[一致しない]** オプションによって、「Cookie Parameter (Cookie パラメーター)」一致条件が満たされるための条件が決まります。
- **[一致する]**: この一致条件に定義された 1 つ以上の値と一致する値を持つ特定の Cookie が、要求に含まれている必要があります。
- **[一致しない]**: 要求が以下のいずれかの条件を満たすことを必要とします。
  - 指定した Cookie を含まない。
  - 指定した Cookie を含むが、値はこの一致条件に定義されたいずれの値とも一致しない。
  
重要な情報: 
- Cookie 名:  
  - Cookie 名を指定する場合、アスタリスクを含む特殊文字はサポートされません。 これは、正確な Cookie 名の一致のみが比較の対象となることを意味します。
  - この一致条件のインスタンスごとに、1 つの Cookie 名のみを指定できます。
  - Cookie 名の比較では、大文字と小文字は区別されません。
- Cookie 値:  
  - 複数の Cookie 値を指定する場合は、値ごとに単一の空白で区切ります。
  - Cookie 値には、特殊文字を利用できます。 
  - ワイルドカード文字が指定されなかった場合は、完全一致のみがこの一致条件を満たします。 たとえば、"Value" を指定すると "Value" と一致し、"Value1" や "Value2" とは一致しません。
  - **[大文字小文字を区別しない]** オプションは、要求の Cookie 値に対して大文字と小文字を区別した比較が行われるかどうかを決定します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie Parameter Regex (Cookie パラメーターの正規表現)
「Cookie Parameter Regex (Cookie パラメーターの正規表現)」一致条件では、Cookie の名前と値を定義します。 正規表現を使用して、必要な Cookie 値を定義できます。 

**[一致する]**/**[一致しない]** オプションによって、この一致条件が満たされる条件が決まります。
- **[一致する]**: 指定した正規表現と一致する値を持つ特定の Cookie が、要求に含まれている必要があります。
- **[一致しない]**: 要求が以下のいずれかの条件を満たすことを必要とします。
  - 指定した Cookie を含まない。
  - 指定した Cookie を含むが、値は指定した正規表現と一致しない。
  
重要な情報: 
- Cookie 名:  
  - Cookie 名を指定する場合、正規表現およびアスタリスクなどの特殊文字はサポートされません。 これは、正確な Cookie 名の一致のみが比較の対象となることを意味します。
  - この一致条件のインスタンスごとに、1 つの Cookie 名のみを指定できます。
  - Cookie 名の比較では、大文字と小文字は区別されません。
- Cookie 値:  
  - Cookie 値には、正規表現を利用できます。
  - **[大文字小文字を区別しない]** オプションは、要求の Cookie 値に対して大文字と小文字を区別した比較が行われるかどうかを決定します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Country
国コードを利用して、国を指定できます。 要求の送信元の国が指定された値と "一致する" または "一致しない" 場合に、この条件が満たされるかを指示するためのオプションが提供されています。

重要な情報: 
- 複数の国コードを指定する場合は、コードごとに単一の空白で区切ります。
- 国コードを指定している場合、ワイルドカードはサポートされません。
- "EU" および "AP" 国コードは、それらのリージョンにあるすべての IP アドレスを包むわけではありません。
- 特定の要求は、有効な国コードを返さない場合があります。 疑問符 (?) は、有効な国コードを特定できなかった要求と一致します。
- 国コードでは大文字と小文字が区別されます。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Customer Origin (顧客配信元)

重要な情報:  
- 「Customer Origin (顧客配信元)」一致条件は、コンテンツが Content Delivery Network URL 経由で要求されたか、または選択した顧客配信元を参照するエッジ CNAME URL 経由で要求されたかどうかに関係なく、満たされます。
- ルールで参照される顧客配信元の構成は、[Customer Origin]\(顧客配信元\) ページから削除できません。 顧客配信元の構成を削除しようとしている場合は、事前に以下の構成で参照していないことを確認してください。
  - 「Customer Origin (顧客配信元)」一致条件
  - エッジ CNAME 構成
- AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「Customer Origin (顧客配信元 配信元)」一致条件と「CDN (CDN 配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「Customer Origin (顧客配信元)」一致条件を AND IF ステートメントで結合することはできません。

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>デバイス

この「Device (デバイス)」一致条件では、モバイル デバイスからの要求をそのプロパティに基づいて識別します。 モバイル デバイスの検出は [WURFL](http://wurfl.sourceforge.net/) から実現します。 次の表に、Content Delivery Network ルール エンジンの WURFL 機能と変数の一覧を示します。
<br>
> [!NOTE] 
> 次の変数は、**Modify Client Request Header (クライアント要求ヘッダーの修正)** と **Modify Client Response Header (クライアント応答ヘッダーの修正)** 機能でサポートされています。

機能 | 変数 | [説明] | サンプルの値
-----------|----------|-------------|----------------
Brand Name | % {wurfl_cap_brand_name} | デバイスのブランド名を示す文字列です。 | Samsung
Device OS | % {wurfl_cap_device_os} | デバイスにインストールされているオペレーティング システムを示す文字列です。 | IOS
Device OS Version | % {wurfl_cap_device_os_version} | デバイスにインストールされているオペレーティング システムのバージョン番号を示す文字列です。 | 1.0.1
Dual Orientation | % {wurfl_cap_dual_orientation} | デバイスが双方向の向きをサポートするかどうかを示すブール値です。 | true
HTML Preferred DTD | % {wurfl_cap_html_preferred_dtd} | HTML コンテンツ向けにモバイル デバイスで推奨されるドキュメント型定義 (DTD) を示す文字列です。 | なし<br/>xhtml_basic<br/>html5
Image Inlining | % {wurfl_cap_image_inlining} | デバイスが Base64 でエンコードされたイメージをサポートするかどうかを示すブール値です。 | false
Is Android | % {wurfl_vcap_is_android} | デバイスが Android OS を使用するかどうかを示すブール値です。 | true
Is IOS | % {wurfl_vcap_is_ios} | デバイスが iOS を使用するかどうかを示すブール値です。 | false
Is Smart TV | % {wurfl_cap_is_smarttv} | デバイスがスマート TV であるかどうかを示すブール値です。 | false
Is Smartphone | % {wurfl_vcap_is_smartphone} | デバイスがスマートフォンであるかどうかを示すブール値です。 | true
Is Tablet | % {wurfl_cap_is_tablet} | デバイスがタブレットであるかどうかを示すブール値です。 これは、OS に依存しない説明です。 | true
Is Wireless Device | % {wurfl_cap_is_wireless_device} | デバイスがワイヤレス デバイスとして認識されているかどうかを示すブール値です。 | true
Marketing Name | % {wurfl_cap_marketing_name} | デバイスのマーケティング名を示す文字列です。 | BlackBerry 8100 Pearl
Mobile Browser | % {wurfl_cap_mobile_browser} | デバイスからコンテンツの要求に使用するブラウザーを示す文字列です。 | Chrome
Mobile Browser Version | % {wurfl_cap_mobile_browser_version} | デバイスからコンテンツの要求に使用するブラウザーのバージョンを示す文字列です。 | 31
Model Name | % {wurfl_cap_model_name} | デバイスのモデル名を示す文字列です。 | s3
プログレッシブ ダウンロード | %{wurfl_cap_progressive_download} | デバイスが、ダウンロード中のオーディオおよびビデオの再生をサポートしているかどうかを示すブール値です。 | true
リリース日 | %{wurfl_cap_release_date} | デバイスが WURFL データベースに追加された年月を示す文字列です。<br/><br/>形式: `yyyy_mm` | 2013_december
Resolution Height | % {wurfl_cap_resolution_height} | デバイスの高さ (ピクセル単位) を示す整数です。 | 768
Resolution Width | % {wurfl_cap_resolution_width} | デバイスの幅 (ピクセル単位) を示す整数です。 | 1024

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Edge Cname (エッジ CNAME)
重要な情報:  
- 利用可能なエッジ CNAME の一覧は、HTTP ルール エンジンが構成されたプラットフォームに対応する [Edge Cname]\(エッジ Cname\) ページで構成済みのものに限られています。
- エッジ CNAME 構成を削除しようとしている場合は、事前に「Edge Cname (エッジ CNAME)」一致条件でその構成を参照していないことを確認してください。 規則で定義されたエッジ CNAME 構成は、[Edge Cname]\(エッジ Cname\) ページから削除できません。 
- AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「Edge Cname (エッジ Cname)」一致条件と「Customer Origin (顧客配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「Edge Cname (エッジ Cname)」一致条件を AND IF ステートメントで結合することはできません。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Referring Domain (参照ドメイン)
コンテンツが要求されたときの参照元に関連付けられたホスト名によって、「Referring Domain (参照ドメイン) 」条件に一致するかどうかが決まります。 ホスト名の参照元が指定された値と "一致する" または "一致しない" 場合に、この条件が満たされるかを指示するためのオプションが提供されています。

重要な情報: 
- 複数のホスト名を指定する場合は、ホスト名ごとに単一の空白で区切ります。
- この一致条件では、特殊文字をサポートします。
- 指定した値にアスタリスクが含まれていない場合、値が参照元のホスト名と完全に一致している必要があります。 たとえば、"mydomain.com"　と指定すると、"www.mydomain.com"　とは一致しません。
- **[大文字小文字を区別しない]** オプションは、大文字と小文字を区別した比較が行われるかどうかを決定します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Request Header Literal (要求ヘッダーのリテラル)
**[一致する]**/**[一致しない]** オプションによって、この一致条件が満たされる条件が決まります。
- **[一致する]**: 要求に、指定したヘッダーが含まれている必要があります。 値は、この一致条件に定義された値と一致している必要があります。
- **[一致しない]**: 要求が以下のいずれかの条件を満たすことを必要とします。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値はこの一致条件に定義された値とは一致しない。
  
重要な情報: 
- ヘッダー名の比較では常に、大文字と小文字は区別されません。 **[大文字小文字を区別しない]** オプションは、ヘッダー値の比較における大文字と小文字の区別を決定します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Request Header Regex (要求ヘッダーの正規表現)
**[一致する]**/**[一致しない]** オプションは、「Request Header Regex (要求ヘッダーの正規表現)」一致条件が満たされる条件を決定します。
- **[一致する]**: 要求に、指定したヘッダーが含まれている必要があります。 値は、指定した正規表現で定義されたパターンと一致している必要があります。
- **[一致しない]**: 要求が以下のいずれかの条件を満たすことを必要とします。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値は指定した正規表現と一致しない。

重要な情報: 
- ヘッダー名:  
  - ヘッダー名の比較では、大文字と小文字は区別されません。
  - ヘッダー名の空白は、"%20"　に置き換える必要があります。 
- ヘッダー値:  
  - ヘッダー値には、正規表現を利用できます。
  - **[大文字小文字を区別しない]** オプションは、ヘッダー値の比較における大文字と小文字の区別を決定します。
  - 正確なヘッダー値が、指定された 1 つ以上のパターンと一致する場合にのみ、この条件が満たされます。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale) 

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Request Header Wildcard (要求ヘッダーのワイルドカード)
**[一致する]**/**[一致しない]** オプションによって、「Request Header Wildcard (要求ヘッダーのワイルドカード)」一致条件が満たされるための条件が決まります。
- **[一致する]**: 要求に、指定したヘッダーが含まれている必要があります。 値は、この一致条件で定義されている 1 つ以上の値と一致する必要があります。
- **[一致しない]**: 要求が以下のいずれかの条件を満たすことを必要とします。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値は指定した値と一致しない。
  
重要な情報: 
- ヘッダー名:  
  - ヘッダー名の比較では、大文字と小文字は区別されません。
  - ヘッダー名の空白は、"%20"　に置き換える必要があります。 この値を使用して、ヘッダー値に空白を指定することもできます。
- ヘッダー値:  
  - ヘッダー値には、特殊文字を利用できます。
  - **[大文字小文字を区別しない]** オプションは、ヘッダー値の比較における大文字と小文字の区別を決定します。
  - 正確なヘッダー値が、指定された 1 つ以上のパターンと一致する場合にのみ、この条件が満たされます。
  - 複数の値を指定する場合は、値ごとに単一の空白で区切ります。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Request Method (要求メソッド)
選択した要求メソッドで要求された資産のみが、「Request Method (要求メソッド)」条件を満たします。 使用可能な要求メソッドは次のとおりです。
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- 削除 
- TRACE 
- CONNECT 

重要な情報: 
- 既定では、GET 要求メソッドのみが、ネットワーク上にキャッシュされたコンテンツを生成できます。 その他のすべての要求メソッドは、ネットワーク経由でプロキシ化されます。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Request Scheme (要求スキーム)
選択したプロトコル経由で要求された資産のみが、「Request Scheme (要求スキーム)」条件を満たします。 使用可能なプロトコルは HTTP および HTTPS です。

重要な情報: 
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>次の手順
* [Azure Content Delivery Network の概要](cdn-overview.md)
* [ルール エンジンのリファレンス](cdn-rules-engine-reference.md)
* [ルール エンジンの条件式](cdn-rules-engine-reference-conditional-expressions.md)
* [ルール エンジンの機能](cdn-rules-engine-reference-features.md)
* [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-rules-engine.md)

