


**ドキュメント最終更新日時**: 1 月 22 日、午後 3:00 PST

予測実行のサイドチャネル攻撃として知られる [CPU 脆弱性の新しいクラス](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)の最近の公開では、顧客がよりわかりやすい説明を求めていることがわかりました。  

Azure を実行し、顧客ワークロードを互いに分離するインフラストラクチャは保護されています。  つまり、Azure を使用する他の顧客は、これらの脆弱性を使用してアプリケーションを攻撃することはできません。

> [!NOTE] 
> 2018 年 1 月 3 日に発表した Azure の軽減策は、Intel で最近[更新されたガイダンス](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/)の影響を受けません。 この新しい情報の結果として、お客様の VM に対する追加のメンテナンス アクティビティはありません。
>
> ハードウェア ベンダーからマイクロコードの更新プログラムを受け取った場合は、これらのベスト プラクティスを今後も更新する予定です。 そのため、最新のガイダンスを確認してください。
>

## <a name="keeping-your-operating-systems-up-to-date"></a>オペレーティング システムを最新の状態に保つ

Azure で実行するアプリケーションを、Azure を使用する他の顧客から切り離すために OS 更新は必要ありませんが、OS のバージョンを常に最新に保つことをお勧めします。 

次のサービスでは、オペレーティング システムを更新するために次の操作が推奨されます。 

<table>
<tr>
<th>サービス</th> <th>推奨される操作 </th>
</tr>
<tr>
<td>Azure クラウド サービス </td>  <td>自動更新を有効にするか、最新のゲスト OS を実行していることを確認します。</td>
</tr>
<tr>
<td>Azure Linux Virtual Machines</td> <td>使用可能な場合、オペレーティング システムのプロバイダーからの更新プログラムをインストールします。 </td>
</tr>
<tr>
<td>Azure Windows Virtual Machines </td> <td>OS の更新プログラムをインストールする前に、サポートされているウイルス対策アプリケーションを実行します。 互換性については詳しくは、ウイルス対策ソフトウェア ベンダーにお問い合わせください。<p> [1 月のセキュリティ ロールアップ](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)をインストールします。 </p></td>
</tr>
<tr>
<td>その他の Azure PaaS サービス</td> <td>これらのサービスを使用している顧客がとる必要のある操作はありません。 Azure では、お使いの OS バージョンが自動的に最新に保たれます。 </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>信頼できないコードを実行する場合の追加のガイダンス 

信頼できないコードを実行していない限り、顧客がとる必要のある追加の操作はありません。 信頼できないコードを許可する場合は (たとえば、お客様がアプリケーション内のクラウドで実行するバイナリまたはコード スニペットのアップロードをお客様の顧客に許可する場合など)、次の追加の手順が必要になります。  


### <a name="windows"></a>Windows 
Windows を使用して、信頼できないコードをホストする場合、予測実行のサイドチャネルの脆弱性に対する追加の保護を提供する Kernel Virtual Address (KVA) Shadowing と呼ばれる Windows の機能を有効にする必要もあります。 この機能は既定ではオフになっており、有効にすると、パフォーマンスに影響が出る場合があります。 サーバーで保護を有効にする方法については、[Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) を参照してください。 Azure Cloud Services を実行している場合は、WA-GUEST-OS-5.15_201801-01 または WA-GUEST-OS-4.50_201801-01 (1 月 10 日から利用可能) を実行していることを確認し、スタートアップ タスクを使用してレジストリ キーを有効にします。


### <a name="linux"></a>Linux
Linux を使用していて、信頼できないコードをホストしている場合は、カーネルによって使用されるページ テーブルをユーザー スペースに属すページ テーブルから分離する Kernel Page Table Isolation (KPTI) を実装するより新しいバージョンに更新する必要があります。 これらの軽減策では Linux OS 更新が必要で、利用可能な場合は、ディストリビューション プロバイダーから取得できる場合があります。 OS プロバイダーは、保護が既定で有効か無効かを判断できます。



## <a name="next-steps"></a>次の手順

詳しくは、[CPU の脆弱性からの Azure 顧客の保護](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)に関する記事を参照してください。
