<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース<sup>1</sup></th>
   <th align="left" valign="middle">既定の制限</th>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの容量 (TB)</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一の BLOB コンテナー、テーブル、キューの最大サイズ</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数</p></td>
   <td valign="middle"><p>制限はストレージ アカウントの容量のみ (500 TB)</p></td>
</tr>
<tr>
   <td valign="middle"><p>ファイル共有の最大サイズ</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>ファイル共有内の最大ファイル数</p></td>
   <td valign="middle"><p>制限は、ファイル共有の合計容量のみ (5 TB)</p></td>
</tr>
<tr>
   <td valign="middle"><p>永続ディスクあたり最大 8 KB IOPS (Basic 階層)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>永続ディスクあたり最大 8 KB IOPS (Standard 階層)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの合計要求レート (オブジェクト サイズは 1KB とする)</p></td>
   <td valign="middle"><p>毎秒最大 20,000 エンティティ (またはメッセージ)</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一 BLOB のターゲット スループット</p></td>
   <td valign="middle"><p>毎秒最大 60 MB または毎秒最大 500 要求</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一キューのターゲット スループット (1 KB のメッセージ)</p></td>
   <td valign="middle"><p>毎秒最大 2000 メッセージ</p></td>
</tr>
<tr>
   <td valign="middle"><p>単一テーブル パーティションのターゲット スループット (1 KB のエンティティ)</p></td>
   <td valign="middle"><p>毎秒最大 2000 エンティティ</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大受信速度 (米国リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 10 Gbps、LRS の場合は 20 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大送信速度 (米国リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 20 Gbps、LRS の場合は 30 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大受信速度 (ヨーロッパおよびアジア リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 5 Gbps、LRS の場合は 10 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大送信速度 (ヨーロッパおよびアジア リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 10 Gbps、LRS の場合は 15 Gbps</p></td>
</tr>
</table>

<sup>1</sup>これらの制限の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../articles/storage/storage-scalability-targets.md)」を参照してください。

<sup>2</sup>Basic 階層の仮想マシンの場合、合計要求レート制限 20,000 に達するのを避けるためには、使用頻度の高い VHD がストレージ アカウントあたり 66 個を超えないようにしてください (20,000/300)。Standard 階層の仮想マシンの場合、使用頻度の高い VHD がストレージ アカウントあたり 40 個を超えないようにしてください (20,000/500)。詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。

<sup>3</sup>GRS は [Geo Redundant Storage (地理冗長ストレージ)](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx) の略です。LRS は [Locally Redundant Storage (ローカル冗長ストレージ)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx) の略です。GRS はローカル冗長でもあることに注意してください。

<!---HONumber=62-->