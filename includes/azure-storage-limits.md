<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
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
   <td valign="middle"><p>永続ディスクあたり最大 8 KB IOPS (Basic レベルの仮想マシン)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>永続ディスクあたり最大 8 KB IOPS (Standard レベルの仮想マシン)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの合計要求レート (オブジェクト サイズは 1KB とする)</p></td>
   <td valign="middle"><p>最大 20,000 の IOPS、エンティティ/秒、またはメッセージ/秒</p></td>
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
   <td valign="middle"><p>単一 ファイル共有のターゲット スループット (プレビュー)</p></td>
   <td valign="middle"><p>最大 60 MB/秒 </p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大受信速度<sup>2</sup> (米国リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 10 Gbps、LRS の場合は 20 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大送信速度<sup>2</sup> (米国リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 20 Gbps、LRS の場合は 30 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大受信速度<sup>2</sup> (ヨーロッパおよびアジア リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 5 Gbps、LRS の場合は 10 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>ストレージ アカウントあたりの最大送信速度<sup>2</sup> (ヨーロッパおよびアジア リージョン)</p></td>
   <td valign="middle"><p>GRS<sup>3</sup> が有効な場合は 10 Gbps、LRS の場合は 15 Gbps</p></td>
</tr>
</table>

<sup>1</sup>ストレージ アカウントの合計要求レート制限は 20,000 IOPS です。仮想マシンがディスクごとの最大 IOPS を利用する場合は、調整を回避するため、すべての仮想マシンの VHD 間での合計 IOPS がストレージ アカウントの制限 (20,000 IOPS) を超えないことを確認してください。

トランザクションの上限に基づいて、単一のストレージ アカウントでサポートされる使用率の高いディスクの数を概算することができます。たとえば、Basic レベルの VM では、使用率の高いディスクの最大数は約 66 (ディスクあたり 20,000/300 IOPS) であり、Standard レベルの VM では約 40 (ディスクあたり IOPS 20,000/500) です。ただし、すべてのディスクが同時に高い率で使用されていなければ、ストレージ アカウントはより多くのディスクをサポートできることに注意してください。

<sup>2</sup>受信とはストレージ アカウントに送信されるすべてのデータ (要求) のことです。*送信*とはストレージ アカウントから送信されるすべてのデータ (応答) のことです。

<sup>3</sup>GRS は地理冗長ストレージのことであり、LRS はローカル冗長ストレージのことです。

<!---HONumber=August15_HO6-->