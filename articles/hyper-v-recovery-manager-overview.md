<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Azure Site Recovery の概要



Azure Site Recovery をデプロイして、System Center Virtual Machine Manager (VMM) クラウド内に配置されている Hyper-V ホスト サーバー上で実行する仮想マシンを保護します。Azure Site Recovery は、以下の 2 つの方法でデプロイできます。

-   **内部設置型サイト間での保護** — 1 つの内部設置型サイトから別の内部設置型サイトに仮想マシンをレプリケートします。Azure Site Recovery コンテナーに保護設定を構成し、有効にします。仮想マシンのデータはソースの Hyper-V ホスト サーバーからターゲットのホスト サーバーにレプリケートされます。Azure Site Recovery がこのプロセスを調整します。このシナリオのチュートリアルは「[Azure の Hyper-V 回復マネージャーの構成][Azure の Hyper-V 回復マネージャーの構成]」で利用可能です。
-   **内部設置型サイトと Azure 間での保護** — 1 つの内部設置型サイトから Microsoft Azure に仮想マシンをレプリケートします。Azure Site Recovery コンテナーに保護設定を構成し、有効にします。Azure Site Recovery はこのプロセスを調整し、レプリケートされた仮想マシンのデータは Azure ストレージに格納されます。このシナリオのチュートリアルは「[Azure Site Recovery のデプロイ: オンプレミスと Azure 間の保護][Azure Site Recovery のデプロイ: オンプレミスと Azure 間の保護]」で利用可能です。

次の表に、この 2 つのデプロイメントのオプションを要約して示します。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">機能</th>
<th align="left">内部設置型と Azure 間</th>
<th align="left">内部設置型サイト間</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure Site Recovery へのデータ</td>
<td align="left">クラウドのメタデータは Azure Site Recovery に送信されます。レプリケートされたデータは Azure ストレージに格納されます。</td>
<td align="left">クラウドのメタデータは Azure Site Recovery に送信されます。レプリケートされたデータはターゲットの Hyper-V ホスト サーバーに格納されます。</td>
</tr>
<tr class="even">
<td align="left">コンテナーの要件</td>
<td align="left"><p>コンテナーには、<a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">コンテナーの要件</a>に適合した証明書が必要です。</p>
<p>コンテナー キーが自動生成されます。このキーによって VMM サーバー上の Azure Site Recovery Provider と Azure Site Recovery との間のトラフィックの整合性が保たれます。</p></td>
<td align="left"><p>コンテナーには、<a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">コンテナーの要件</a>に適合した証明書が必要です。</p>
<p>コンテナー キーが自動生成されます。このキーによって VMM サーバー上の Azure Site Recovery Provider と Azure Site Recovery との間のトラフィックの整合性が保たれます。</p></td>
</tr>
<tr class="odd">
<td align="left">レプリケーション</td>
<td align="left">仮想マシンが内部設置型の Hyper-V サーバーから Azure ストレージにレプリケートされます。</td>
<td align="left">仮想マシンが 1 つの内部設置型の Hyper-V サーバーから別のサーバーにレプリケートされます。</td>
</tr>
<tr class="even">
<td align="left">仮想マシンのストレージ</td>
<td align="left">Azure ストレージに格納されている仮想マシンのハード ディスク</td>
<td align="left">Hyper-V ホストに格納されている仮想マシンのハード ディスク</td>
</tr>
<tr class="odd">
<td align="left">Azure ストレージ</td>
<td align="left"><p>Azure ストレージのアカウントが必要です。</p>
<p>アカウントはジオ (主要地域) レプリケーションが有効で Azure Site Recovery サービスと同じリージョンに含まれている必要があります。また、同じサブスクリプションに関連付けられている必要があります。</p></td>
<td align="left">適用不可</td>
</tr>
<tr class="even">
<td align="left">VMM サーバー</td>
<td align="left">ソースの VMM サーバーのみ必要</td>
<td align="left">ソースとターゲットの VMM サーバーがあり、それぞれに少なくとも 1 つのクラウドがあるか、または単一の VMM サーバーに 2 つのクラウドが配置されている必要があります。</td>
</tr>
<tr class="odd">
<td align="left">VMM サーバーの System Center バージョン</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Azure Site Recovery Provider</td>
<td align="left">ソースの VMM サーバーにインストール</td>
<td align="left">ソースとターゲットの VMM サーバーにインストール</td>
</tr>
<tr class="odd">
<td align="left">Azure Recovery Services Agent</td>
<td align="left">VMM クラウドに配置されている Hyper-V ホスト サーバーにインストール</td>
<td align="left">必要なし</td>
</tr>
<tr class="even">
<td align="left">仮想マシンの復旧ポイント</td>
<td align="left"><p>時間で復旧ポイントを設定。</p>
<p>保持する必要がある復旧ポイントの期間 (0 ～ 24 時間) を指定します。復旧ポイントは、最初の 1 時間の間は 90/copy_frequency という式に基づいて作成され、その後は 1 時間に 1 回作成されます。</p></td>
<td align="left"><p>数量で復旧ポイントを設定。</p>
<p>保持する必要がある追加の復旧ポイント数 (0 ～ 15) を指定します。既定では、復旧ポイントは 1 時間ごとに作成されます。ゼロを設定すると、直前の復旧ポイントだけがレプリカの Hyper-V ホスト サーバーに格納されます。</p></td>
</tr>
<tr class="odd">
<td align="left">ネットワーク マッピング</td>
<td align="left"><p>VM ネットワークを Azure ネットワークにマッピングします。</p>
<p>ネットワーク マッピングにより、同じソース VM ネットワークでフェールオーバーするすべての仮想マシンが、フェールオーバー後に接続可能になります。さらに、ターゲット Azure ネットワーク上にネットワーク ゲートウェイが設定されている場合、仮想マシンは内部設置型仮想マシンに接続できます。</p>
<p>マッピングが有効でない場合は、同じ復旧計画でフェールオーバーする仮想マシンのみ、Azure へのフェールオーバー後に相互接続できます。</p></td>
<td align="left"><p>ソース VM ネットワークをターゲット VM ネットワークにマッピングします。</p>
<p>ネットワーク マッピングを使用すると、レプリケートされた仮想マシンが最適な Hyper-V ホスト サーバーに配置されます。また、ソース VM ネットワークに関連付けられている仮想マシンは、フェールオーバー後に、マップされたターゲット ネットワークに確実に関連付けられます。</p>
<p>マッピングが有効に設定されていないと、レプリケートされた仮想マシンがネットワークに接続されません。</p></td>
</tr>
<tr class="even">
<td align="left">ストレージ マッピング</td>
<td align="left">適用不可</td>
<td align="left"><p>ソース VMM サーバー上のストレージ分類を、ターゲット VMM サーバーのストレージ分類にマッピングします。</p>
<p>マッピングが有効な場合、ソース ストレージ分類の仮想マシンのハード ディスクは、フェールオーバー後に、ターゲット ストレージ分類に配置されます。</p>
<p>ストレージ マッピングを有効にしないと、レプリケートされた仮想ハード ディスクはターゲット Hyper-V ホスト サーバーの既定の場所に格納されます。</p></td>
</tr>
</tbody>
</table>

疑問がある場合は、「[Azure Recovery Services Forum (Azure 復旧サービス フォーラム)][Azure Recovery Services Forum (Azure 復旧サービス フォーラム)]」にアクセスしてください。

</div>

  [Azure の Hyper-V 回復マネージャーの構成]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Azure Site Recovery のデプロイ: オンプレミスと Azure 間の保護]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Azure Recovery Services Forum (Azure 復旧サービス フォーラム)]: http://go.microsoft.com/fwlink/?LinkId=313628
