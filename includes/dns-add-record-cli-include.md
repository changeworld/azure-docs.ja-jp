#### <a name="create-an-a-record-set-with-single-record"></a>1 つのレコードを含む A レコード セットの作成

レコード セットを作成するには、 `azure network dns record-set create`を使用します。 リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類、有効期間 (TTL) を指定します。

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

A レコード セットを作成した後、 `azure network dns record-set add-record`でレコード セットに IPv4 アドレスを追加します。

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>1 つのレコードを含む AAAA レコード セットの作成

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>1 つのレコードを含む CNAME レコード セットの作成

CNAME レコードでは、単一の文字列値だけを使用できます。


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>1 つのレコードを含む MX レコード セットの作成

この例では、レコード セット名 "@" を使用してゾーンの頂点 (この場合は "contoso.com" ) に MX レコードを作成します。 これは、MX レコードに共通です。

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>1 つのレコードを含む NS レコード セットの作成

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>1 つのレコードを含む PTR レコード セットの作成  
ここで ' my-arpa-zone.com' は IP 範囲を表す ARPA ゾーンを表します。  このゾーンの各 PTR レコード セットは、この IP の範囲内の IP アドレスに対応します。    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>1 つのレコードを含む SRV レコード セットの作成

ゾーンのルートに SRV レコードを作成する場合、レコード名で "_service" と "_protocol" を指定できます。 レコード名に "@" を含める必要はありません。


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>1 つのレコードを含む TXT レコード セットの作成

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"


<!--HONumber=Oct16_HO2-->


