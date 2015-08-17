<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>1 つのサブスクリプション内の Azure Media Services (AMS) アカウント数</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS アカウントあたりのアセット数</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1,000,000</p></td>
</tr>
<tr>
   <td valign="middle"><p>ジョブあたりのチェーン タスク数</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>タスクあたりのアセット数</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>ジョブあたりのアセット数</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS アカウントあたりのジョブ数 </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50,000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>一度に 1 つのアセットに関連付けられる一意のロケーター数</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS アカウントあたりのライブ チャネル数 </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>チャネルあたりの停止状態のプログラム数 </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>チャネルあたりの実行状態のプログラム数 </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>AMS アカウントあたりの実行状態のストリーミング エンドポイント数</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>ストリーミング エンドポイントあたりのストリーミング ユニット数 </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>AMS アカウントあたりの Encoding ユニット数 </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>サポート チケットを開き、このクォータの制限を更新することを要求できます。上限を上げる目的で、サポート チケットを送信する代わりに、AMS アカウントを追加作成することはやめてください。

<sup>2</sup>この数には、キューに置かれたジョブ、終了したジョブ、アクティブなジョブ、取り消されたジョブが含まれています。これには、削除されたジョブは含まれません。**IJob.Delete** か **DELETE** HTTP 要求を使用して古いジョブを削除できます。

<sup>3</sup>Job エンティティの一覧を要求すると、要求ごとに最大 1,000 件が返されます。送信したすべてのジョブを追跡する必要がある場合は、「[OData システム クエリ オプション](http://msdn.microsoft.com/library/gg309461.aspx)」の説明に従って top/skip を使うことができます。

<sup>4</sup>ロケーターはユーザーごとのアクセス制御を管理するようには設計されていません。個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。

<!---HONumber=August15_HO6-->