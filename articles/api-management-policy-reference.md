<properties 
	pageTitle="Azure API Management ポリシー リファレンス" 
	description="API Management の構成に使用できるポリシーについて説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Azure API Management ポリシー リファレンス

このトピックでは、次の API Management (プレビュー) ポリシーに関するリファレンス情報を示します。ポリシーを追加および構成する方法については、「[Azure API Management のポリシー][Azure API Management のポリシー]」を参照してください。

-   [アクセス制限ポリシー][アクセス制限ポリシー]
    -   [使用量クォータ][使用量クォータ] - 更新可能な呼び出しボリューム、有効期間中の呼び出しボリューム、または帯域幅クォータを適用できます。
    -   [レート制限][レート制限] - 呼び出しまたは帯域幅の消費レートを制限して、API 使用量の急激な増加を防止します。
    -   [呼び出し元 IP 制限][呼び出し元 IP 制限] - 特定の IP アドレスまたはアドレス範囲からの呼び出しをフィルター処理 (許可、拒否) します。
-   [コンテンツ変換ポリシー][コンテンツ変換ポリシー]
    -   [HTTP ヘッダーの設定][HTTP ヘッダーの設定] - 既存の応答または要求ヘッダーまたは新しい応答または要求ヘッダーに値を割り当てます。
    -   [XML から JSON への変換][XML から JSON への変換] - XML からの要求本文または応答本文を "JSON" 形式または JSON の "XML に忠実な" 形式に変換します。
    -   [本文中の文字列の置換][本文中の文字列の置換] - 要求または応答内の部分文字列を検索して別の部分文字列で置き換えます。
    -   [クエリ文字列パラメーターの設定][クエリ文字列パラメーターの設定] - 要求クエリ文字列パラメーターの追加、値の置換、または削除を行います。
-   [キャッシュ ポリシー][キャッシュ ポリシー]
    -   [キャッシュに格納][キャッシュに格納] - 指定されたキャッシュ構成に従って応答をキャッシュに格納します。
    -   [キャッシュから取得][キャッシュから取得] - キャッシュを検索して、キャッシュに格納された有効な応答があればそれを返します。
-   [その他のポリシー][その他のポリシー]
    -   [URI の書き換え][URI の書き換え] - 要求 URL をパブリックな形式から Web サービスで求められる形式に変換します。
    -   [コンテンツの URLS のマスク][コンテンツの URLS のマスク] - 応答本文および場所ヘッダー内のリンクを書き直して (マスクして)、これらがプロキシを経由して同等のリンクを指し示すようにします。
    -   [クロスドメイン呼び出しを許可][クロスドメイン呼び出しを許可] - Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。
    -   [JSONP][JSONP] - JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。
    -   [CORS][CORS] - クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。

## <a name="access-restriction-policies"> </a>アクセス制限ポリシー

-   [使用量クォータ][使用量クォータ] - 更新可能な呼び出しボリューム、有効期間中の呼び出しボリューム、または帯域幅クォータを適用できます。
-   [レート制限][レート制限] - 呼び出しまたは帯域幅の消費レートを制限して、API 使用量の急激な増加を防止します。
-   [呼び出し元 IP 制限][呼び出し元 IP 制限] - 特定の IP アドレスまたはアドレス範囲からの呼び出しをフィルター処理 (許可、拒否) します。

### <a name="usage-quota"> </a>使用量クォータ

**説明:**
更新可能な呼び出しボリューム、有効期間中の呼び出しボリューム、または帯域幅クォータを適用します。

**ポリシー ステートメント:**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**例:**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**適用できる場所:**
成果物スコープの受信セクションで使用します。

**適用する必要がある状況:**
成果物に対して操作または API の使用量を制限する必要がある場合。

**適用する必要がある (またはない) 理由:**
時間と帯域幅に基づいて成果物の使用量制限を定義するため。API が定義されているクォータ制限に達すると、それ以降の呼び出しは拒否され、エラー メッセージが表示されます。クォータは、通常、事前に定義された時間間隔で許可される要求メッセージの数および帯域幅制限として定義されます。

| 属性                     | 説明                                                                                                          |
|--------------------------|---------------------------------------------------------------------------------------------------------------|
| quota calls="number"     | renewal-period 中に許可されるサブスクリプション呼び出しの最大数 (例: 10000)。                                 |
| bandwidth="kilobytes"    | 指定された renewal-period 中にこの成果物、API、または操作に対して許可される最大消費量 (KB 単位) (例: 40000)。 |
| renewal-period="seconds" | クォータが適用される期間 (秒単位) (例: 3600)。                                                                |
| api name="name"          | クォータが適用される API 呼び出しの名前。                                                                     |
| calls="number"           | 指定された renewal-period 中に許可される API または操作に対する最大呼び出し回数 (例: 5000)。                  |
| operation name="name"    | クォータが適用される操作の名前。                                                                              |

### <a name="rate-limit"> </a>レート制限

**説明:**
API (およびオプションで特定の API 操作) に渡される要求のレートを制限して、API の使用量が急増するのを防止します。ポリシーがトリガーされると、コンシューマーは `429 Too Many Requests` 応答ステータス コードを受け取ります。

**ポリシー ステートメント:**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**例:**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**適用できる場所:**
成果物スコープの受信セクションで使用します。

| 要素/属性                | 説明                                             |
|--------------------------|--------------------------------------------------|
| calls="number"           | renewal-period 中に許可される呼び出しの回数      |
| renewal-period="seconds" | レート制限呼び出しクォータが更新されるまでの期間 |
| api name="name"          | レート制限が適用される API の名前                |
| operation name="name"    | レート制限が適用される操作の名前                 |

### <a name="caller-ip-restriction"> </a>呼び出し元 IP 制限

**説明:**
特定の IP アドレスまたはアドレス範囲からの呼び出しをフィルター処理 (許可、拒否) します。

**ポリシー ステートメント:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**例:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**適用できる場所:**
任意のスコープの受信セクションで使用します。

**適用する必要がある状況:**
このポリシーは、サービスにアクセスできるユーザーを制御する必要がある場合に使用します。

**適用する必要がある (またはない) 理由:**
このポリシーは、ホストまたはホストの範囲ベースでアクセスに関する厳密な制御が求められるときにのみ必要になります (たとえば、高度なセキュリティ要件があるサービス)。

<table>
<thead>
<tr>
<th>属性</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>ip-filter action=&quot;allow | forbid&quot;</td>
<td>指定されたアドレスに対して呼び出しを許可するかどうかを指定します。</td>
</tr>
<tr>
<td>address=&quot;address&quot;</td>
<td>アクセスを許可または拒否する 1 つまたは複数の IP アドレス。</td>
</tr>
<tr>
<td>address-range from=&quot;address&quot; to=&quot;address&quot;</td>
<td>アクセスを許可または拒否する IP アドレスの範囲。</td>
</tr>
</tbody>
</table>

## <a name="content-transformation-policies"> </a>コンテンツ変換ポリシー

-   [HTTP ヘッダーの設定][HTTP ヘッダーの設定] - 既存の応答または要求ヘッダーまたは新しい応答または要求ヘッダーに値を割り当てます。
-   [XML から JSON への変換][XML から JSON への変換] - XML からの要求本文または応答本文を "JSON" 形式または JSON の "XML に忠実な" 形式に変換します。
-   [本文中の文字列の置換][本文中の文字列の置換] - 要求または応答内の部分文字列を検索して別の部分文字列で置き換えます。
-   [クエリ文字列パラメーターの設定][クエリ文字列パラメーターの設定] - 要求クエリ文字列パラメーターの追加、値の置換、または削除を行います。

### <a name="set-http-header"> </a>HTTP ヘッダーの設定

**説明:**
 既存の応答または要求ヘッダーまたは新しい応答または要求ヘッダーに値を割り当てます。

HTTP ヘッダーのリストを HTTP メッセージに挿入します。受信パイプラインに配置した場合、このポリシーは、ターゲット サービスに渡される要求の HTTP ヘッダーを設定します。送信パイプラインに配置した場合、このポリシーは、プロキシのクライアントに送信される応答の HTTP ヘッダーを設定します。

**ポリシー ステートメント:**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**例:**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**適用できる場所:**
*発行者*以外の任意のスコープの受信セクションと送信セクションで使用します。

**適用する必要がある状況:**
HTTP トランザクションの動作パラメーターまたは戻り値を指定するために使用します。

**適用する必要がある (またはない) 理由:**
ほとんどの HTTP 要求および多くの応答では、入力または応答パラメーターを指定するためにヘッダーを必要とします。したがって、このポリシーは、ほとんどすべてのサービスに適用できます。

<table>
<thead>
<tr>
<th>属性</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>reconcile-action=&quot;override|skip|append&quot;</td>
<td>ヘッダーが既に指定されている場合の操作を指定します。注: &quot;override&quot; に設定した場合、同じ名前の複数のエントリを設定すると、すべてのエントリに従ってヘッダーが設定されます (複数回設定されます)。設定された値のみが結果に設定されます。</td>
</tr>
<tr>
<td>header name=&quot;header name&quot;</td>
<td>設定するヘッダーの名前を指定します。必須。</td>
</tr>
<tr>
<td>exists-action=&quot;override | skip | append&quot;</td>
<td>対象のヘッダーが既に指定されている場合の操作を指定します。</td>
</tr>
<tr>
<td>value=&quot;value&quot;</td>
<td>設定するヘッダーの値を指定します。</td>
</tr>
</tbody>
</table>
### <a name="convert-xml-to-json"> </a>XML から JSON への変換

**説明:**
要求本文または応答本文を XML から JSON に変換します。ML のみのバックエンド Web サービスに基づいて API を近代
**ポリシー ステートメント:**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**例:**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**適用できる場所:**
API または操作スコープの受信セクションまたは送信セクションで使用します。

**適用する必要がある (またはない) 理由:**
XML のみのバックエンド Web サービスに基づいて API を近代化するため。

<table>
<thead>
<tr>
<th>属性</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>kind=&quot;javascript-friendly | direct</td>
<td>変換された JSON は JavaScript 開発者が見慣れた形式になります | 変換された JSON は元の XML ドキュメントの構造を反映します</td>
</tr>
<tr>
<td>apply= always | content-type-xml</td>
<td>常に変換します | <code data-inline="1">Content-Type</code> ヘッダーに XML の存在が示されている場合にのみ変換します</td>
</tr>
<tr>
<td>consider-accept-header=&quot;true | false&quot;</td>
<td><code data-inline="1">Accept</code> ヘッダーの値に基づいて変換を適用します | 常に変換します</td>
</tr>
</tbody>
</table>
### <a name="replace-string-in-body"> </a>本文中の文字列の置換

**説明:**
要求または応答内の部分文字列を検索して別の文字列で置き換えます。

**ポリシー ステートメント:**

    <find-and-replace from="what to replace" to="replacement" />

**例:**

    <find-and-replace from="notebook" to="laptop" />

**適用できる場所:**
任意のスコープの受信セクションと送信セクションで使用します。

| 属性                   | 説明                                 |
|------------------------|--------------------------------------|
| from="what to replace" | 検索する文字列。                     |
| to="replacement"       | 見つかった文字列と置き換える文字列。 |

### <a name="set-query-string-parameter"> </a>クエリ文字列パラメーターの設定

**説明:**
要求クエリ文字列パラメーターの追加、値の置換、または削除を行います。

**ポリシー ステートメント:**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**例:**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**適用できる場所:**
任意のスコープの受信セクションで使用できます。

**適用する必要がある (またはない) 理由:**
オプションであるかまたは要求内に存在しない、バックエンド サービスで期待されるクエリ パラメーターを渡すために使用します。

| 要素/属性                | 説明                                                  |
|--------------------------|-------------------------------------------------------|
| name="name"              | パラメーターの名前を指定する文字列                    |
| exists-action="override" | パラメーターの値を置き換えます (要求内に存在する場合) |
| exists-action="skip"     | パラメーターが要求内に存在する場合は何もしません      |
| exists-action="append"   | 値を既存の要求パラメーターに追加します                |
| exists-action="delete"   | パラメーターを要求から削除します\*                    |
| value="value"            | 囲む要素内のパラメーターの値を設定します              |

## <a name="caching-policies"> </a>キャッシュ ポリシー

-   [キャッシュに格納][キャッシュに格納] - 指定されたキャッシュ構成に従って応答をキャッシュに格納します。
-   [キャッシュから取得][キャッシュから取得] - キャッシュを検索して、キャッシュに格納された有効な応答があればそれを返します。

### <a name="store-to-cache"> </a>キャッシュに格納

**説明:**
指定されたキャッシュ設定に従って応答をキャッシュに格納します。[キャッシュから取得][キャッシュから取得]ポリシーが必要です。

**ポリシー ステートメント:**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**例:**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**適用できる場所:**
API スコープと操作スコープのどちらかまたは両方の送信セクションに使用できます。

**適用する必要がある状況:**
応答の内容が一定期間にわたって静的である場合に適用する必要があります。

**適用する必要がある (またはない) 理由:**
応答のキャッシュを使用すると、バックエンド Web サーバーの帯域幅および処理の要件が低減され、API コンシューマーによって認識される遅延が小さくなります。

<table>
<thead>
<tr>
<th>要素/属性</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>seconds</td>
<td>キャッシュに格納されたエントリの有効期間 (秒単位、既定値 = 3600)</td>
</tr>
<tr>
<td>cache-on | do-not-cache</td>
<td>応答はキャッシュに格納されます | 応答はキャッシュに格納されず、キャッシュに関連するヘッダーはダウンストリーム キャッシュを禁止するよう設定されます</td>
</tr>
</tbody>
</table>

### <a name="get-from-cache"> </a>キャッシュから取得

**説明:**
キャッシュを検索して、キャッシュに格納された有効な応答があればそれを返します。API コンシューマーからのキャッシュ検証要求に適切に応答します。[キャッシュに格納][キャッシュに格納]ポリシーが必要です。

**ポリシー ステートメント:**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**例:**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**適用できる場所:**
API スコープと操作スコープのどちらかまたは両方の受信セクションに使用できます。

**適用する必要がある状況:**
応答の内容が一定期間にわたって静的である場合に適用する必要があります。

**適用する必要がある (またはない) 理由:**
応答のキャッシュを使用すると、バックエンド Web サーバーの帯域幅および処理の要件が低減され、API コンシューマーによって認識される遅延が小さくなります。

<table>
<thead>
<tr>
<th>要素/属性</th>
<th>説明</th>
</tr>
</thead>
<tbody>
<tr>
<td>vary-by-developer=&quot;true | false&quot;</td>
<td>開発者キーごとに応答をキャッシュに格納する場合は true に設定します。既定値は false です</td>
</tr>
<tr>
<td>vary-by-developer-groups=&quot;true | false&quot;</td>
<td>ユーザー ロールごとに応答をキャッシュに格納する場合は true に設定します。既定値は false です</td>
</tr>
<tr>
<td>downstream-caching-type=&quot;none | private | public&quot;</td>
<td>none - ダウンストリーム キャッシュは許可されません。既定値です | private - ダウンストリーム プライベート キャッシュが許可されます |   public - プライベートおよび共有ダウンストリーム キャッシュが許可されます。
</td>
</tr>
<tr>
<td>vary-by-header: &quot;Accept&quot;</td>
<td><code data-inline="1">Accept</code> ヘッダーの値ごとに応答をキャッシュに格納します</td>
</tr>
<tr>
<td>vary-by-header: Accept-Charset&quot;</td>
<td><code data-inline="1">Accept-Charset</code> ヘッダーの値ごとに応答をキャッシュに格納します</td>
</tr>
<tr>
<td>vary-by-header: &quot;header name&quot;</td>
<td>指定されたヘッダー (例: <code data-inline="1">Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match</code>) の値ごとに応答をキャッシュに格納します  </td>
</tr>
<tr>
<td>vary-by-query-parameter: &quot;parameter name&quot;</td>
<td>指定されたクエリ パラメーターの値ごとに応答をキャッシュに格納します。1 つまたは複数のパラメーターを入力します。セミコロンを区切り文字として使用します。パラメーターを指定しない場合、すべてのクエリ パラメーターが使用されます。</td>
</tr>
</tbody>
</table>

## <a name="other-policies"> </a>その他のポリシー

-   [URI の書き換え][URI の書き換え] - 要求 URL をパブリックな形式から Web サービスで求められる形式に変換します。
-   [コンテンツの URLS のマスク][コンテンツの URLS のマスク] - 応答本文および場所ヘッダー内のリンクを書き直して (マスクして)、これらがプロキシを経由して同等のリンクを指し示すようにします。
-   [クロスドメイン呼び出しを許可][クロスドメイン呼び出しを許可] - Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。
-   [JSONP][JSONP] - JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。
-   [CORS][CORS] - クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。

### <a name="rewrite-uri"> </a>URI の書き換え

**説明:**
要求 URL をパブリックな形式から Web サービスで求められる形式に変換します。

**パブリック URL:** <http://api.example.com/storenumber/ordernumber>

**要求 URL:** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>

書き換え URL には追加のテンプレート パス パラメーターを追加しないでください。追加できるのはクエリ文字列パラメーターのみです。

**ポリシー ステートメント:**

    <rewrite-uri template="uri template" />

**例:**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**適用できる場所:**
操作スコープの受信セクションのみ。

**適用する必要がある状況:**
人間やブラウザーにとって使いやすい URL を Web サービスで求められる URL 形式に変換する必要がある場合に使用します。

**適用する必要がある (またはない) 理由:**
代替 URL 形式を公開する場合にのみ適用する必要があります。クリーン URL、RESTful URL、ユーザー フレンドリ URL、または SEO フレンドリ URL は、クエリ文字列を含まず、(スキームと機関の後に) リソースのパスのみを含む、純粋に構造的な URL です。これは、一般的に、美学上、使いやすさ、または検索エンジン最適化 (SEO) の目的で行われます。

| 属性                    | 説明                                                     |
|-------------------------|----------------------------------------------------------|
| template="uri template" | 実際の Web サービス URL と任意のクエリ文字列パラメーター |

### <a name="mask-urls-in-content"> </a>コンテンツの URLS のマスク

**説明:**
応答本文および場所ヘッダー内のリンクを書き直して (マスクして)、これらがプロキシを経由して同等のリンクを指し示すようにします。

**ポリシー ステートメント:**

    <redirect-body-urls />

**例:**

    <redirect-body-urls />

**適用できる場所:**
*API* スコープおよび*操作*スコープで適用します。受信セクションおよび送信セクションで適用できます。

### <a name="allow-cross-domain-calls"> </a>クロスドメイン呼び出しを許可

**説明:**
Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。

**ポリシー ステートメント:**

    <cross-domain />

**例:**

    <cross-domain />

**適用できる場所:**
*発行者*スコープの受信セクションで使用します。

### <a name="jsonp"> </a>JSONP

**説明:**
JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。JSONP は、異なるドメインのサーバーのデータを要求するために JavaScript プログラムで使用される方法です。JSONP を使用すると、Web ページへのアクセスが同じドメイン内である必要があるという、ほとんどの Web ブラウザーで適用される制限を回避できます。

**ポリシー ステートメント:**

    <jsonp callback-parameter-name="callback function name" />

**例:**

    <jsonp callback-parameter-name="cb" />

コールバック パラメーター `?cb=XXX` なしでメソッドを呼び出した場合、(関数呼び出しラッパーのない) プレーン JSON が返されます。

コールバック パラメーター `?cb=XXX` を追加した場合、元の JSON 結果をコールバック関数でラップして結果が返されます (例: `XXX('<json result goes here>')`)。

**適用できる場所:**
送信セクションでのみ使用します。

**適用する必要がある状況:**
異なるドメインのサーバーのデータを要求する場合。

| 属性                    | 説明                                                                                           |
|-------------------------|------------------------------------------------------------------------------------------------|
| callback-parameter-name | 関数が含まれる完全修飾ドメイン名をプレフィックスとするクロスドメイン JavaScript 関数呼び出し。 |

### <a name="cors"> </a>CORS

**説明:**
クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。

CORS を使用すると、ブラウザーとサーバーは、やり取りを介して、特定のクロス オリジン要求 (たとえば、他のドメインに対して Web ページの JavaScript で実行された XMLHttpRequests 呼び出し) を許可するかどうかを決めることができます。これにより、単に同一オリジンの要求を許可するよりも高い柔軟性が得られる一方、すべてのクロス オリジン要求を許可するよりも高いセキュリティを実現できます。

**ポリシー ステートメント:**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**例:**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**適用できる場所:**
受信セクションと *API* スコープおよび*操作*スコープのみで適用します。

| 属性                 | 説明                                                                                                                                            |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| <origin>\*</origin>  | 任意の URI、または 1 つまたは複数の特定の URI のリストを許可します                                                                              |
| <origin>URI</origin> | URI には、スキーム、ホスト、およびポートを含める必要があります。ポートを省略した場合、http 用にポート 80、https 用にポート 443 が使用されます。 |

  [Azure API Management のポリシー]: ../api-management-howto-policies
  [アクセス制限ポリシー]: #access-restriction-policies
  [使用量クォータ]: #usage-quota
  [レート制限]: #rate-limit
  [呼び出し元 IP 制限]: #caller-ip-restriction
  [コンテンツ変換ポリシー]: #content-transformation-policies
  [HTTP ヘッダーの設定]: #set-http-header
  [XML から JSON への変換]: #convert-xml-to-json
  [本文中の文字列の置換]: #replace-string-in-body
  [クエリ文字列パラメーターの設定]: #set-query-string-parameter
  [キャッシュ ポリシー]: #caching-policies
  [キャッシュに格納]: #store-to-cache
  [キャッシュから取得]: #get-from-cache
  [その他のポリシー]: #other-policies
  [URI の書き換え]: #rewrite-uri
  [コンテンツの URLS のマスク]: #mask-urls-in-content
  [クロスドメイン呼び出しを許可]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors

<!--HONumber=46--> 
