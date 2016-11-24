### <a name="create-an-aaaa-record-set-with-a-single-record"></a>1 つのレコードを含む AAAA レコード セットの作成

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>1 つのレコードを含む CNAME レコード セットの作成

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>1 つのレコードを含む MX レコード セットの作成
この例では、レコード セット名 "@" を使用してゾーンの頂点 (この場合は "contoso.com" ) に MX レコードを作成します。 これは、MX レコードに共通です。

```powershell
$rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>1 つのレコードを含む NS レコード セットの作成

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>1 つのレコードを含む PTR レコード セットの作成
ここで ' my-arpa-zone.com' は IP 範囲を表す ARPA ゾーンを表します。  このゾーンの各 PTR レコード セットは、この IP の範囲内の IP アドレスに対応します。

```powershell
$rs = New-AzureRmDnsRecordSet -Name "10" -RecordType PTR -Ttl 3600 -ZoneName my-arpa-zone.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ptrdname "myservice.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>1 つのレコードを含む SRV レコード セットの作成
ゾーンのルートに SRV レコードを作成している場合、レコード名で *_service* と *_protocol* を指定します。 レコード名に "@" を含める必要はありません。

```powershell
$rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="create-a-txt-record-set-with-a-single-record"></a>1 つのレコードを含む TXT レコード セットの作成

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
Set-AzureRmDnsRecordSet -RecordSet $rs
```


<!--HONumber=Nov16_HO3-->


