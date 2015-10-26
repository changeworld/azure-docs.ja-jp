リソース|無料|Shared (プレビュー)|基本|Standard|Premium (プレビュー)</th>
---|---|---|---|---|---
[App Service プラン](web-sites-web-hosting-plan-overview.md)あたりの [Web、モバイル、または API アプリ数](../services/app-service/)<sup>1</sup>|10|100|無制限<sup>2</sup>|無制限<sup>2</sup>|無制限<sup>2</sup>
[App Service プラン](web-sites-web-hosting-plan-overview.md)あたりの[ロジック アプリ数](../services/app-service/)</a><sup>1</sup>|10|10|10|コアあたり 20|コアあたり 20 
[[App Service プラン]](web-sites-web-hosting-plan-overview.md)|リージョンあたり 1 件|リソース グループあたり 10|リソース グループあたり 10|リソース グループあたり 10|リソース グループあたり 10
コンピューティング インスタンスの種類|共有|共有|専用<sup>3</sup>|専用<sup>3</sup>|専用<sup>3</sup></p>
[スケールアウト](web-sites-scale.md) (最大インスタンス)|1 Shared インスタンス|1 Shared インスタンス|3 専用インスタンス<sup>3</sup>|10 専用インスタンス<sup>3</sup>|50 専用インスタンス<sup>3、4</sup>
ストレージ<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4、5</sup></p>
CPU 時間 (1 日)<sup>6</sup>|約 60 分|約 240 分|無制限、Standard の[料金](../pricing/details/app-service/)で課金</a>|無制限、Standard の料金で課金|無制限、Standard の料金で課金
メモリ (1 時間)|App Service プランごとに 1,024 MB|アプリごとに 1,024 MB|該当なし|該当なし|該当なし
帯域幅|165 MB|無制限、[データ転送の料金](../pricing/details/data-transfers/)が適用される|無制限、データ転送の料金が適用される|無制限、データ転送の料金が適用される|無制限、データ転送の料金が適用される
アプリケーションのアーキテクチャ|32 ビット|32 ビット|32 ビット/64 ビット|32 ビット/64 ビット|32 ビット/64 ビット
インスタンスごとの Web ソケット数<sup>7</sup>|5|35|350|無制限|無制限
アプリケーションごとの同時[デバッガー接続](web-sites-dotnet-troubleshoot-visual-studio.md)|1|1|1|5|5
[FTP/S と SSL を利用する azurewebsites.net サブドメイン](web-sites-configure-ssl-certificate.md)|○|○|○|○|○
[カスタム ドメイン](web-sites-custom-domain-name.md)のサポート||○|○|○|○
カスタム ドメインの [SSL サポート](web-sites-configure-ssl-certificate.md)|||無制限|無制限、5 件の SNI SSL 接続と 1 件の IP SSL 接続が含まれる|無制限、5 件の SNI SSL 接続と 1 件の IP SSL 接続が含まれる
統合 Load Balancer||○|○|○|○
[常時接続](web-sites-configure.md)|||○|○|○
[スケジュールされたバックアップ](web-sites-backup.md)||||1 日に 1 回|5 分ごとに 1 回<sup>8</sup>
[自動スケール](web-sites-scale.md)|||○|○|○
[WebJobs](web-sites-create-web-jobs.md)<sup>9</sup>|○|○|○|○|○
[Azure Scheduler](../services/scheduler/) のサポート||○|○|○|○
[エンドポイントの監視](web-sites-monitor.md)|||○|○|○
[ステージング スロット (プレビュー)](web-sites-staged-publishing.md)||||5|20
アプリケーションごとのカスタム ドメイン数</a>||500|500|500|500
SLA||<p>|99\.9%|99\.95%<sup>10</sup>|99\.95%<sup>10</sup>

<sup>1</sup>アプリとストレージのクォータは、特に記述のない限り、App Service プラン単位の数字です。<sup>2</sup>これらのマシンで実際にホストできるアプリの数は、アプリのアクティビティ、マシン インスタンスのサイズ、対応するリソース使用量によって異なります。<sup>3</sup>専用インスタンスのサイズはさまざまです。詳細については、「[App Service 料金](/pricing/details/app-service/)」を参照してください。サポート要求を行って、インスタンスを追加することもできます。<sup>4</sup>Premium レベルでは、App Service 環境の使用時に最大 50 のコンピューティング インスタンス (可用性による) と 500 GB のディスク領域を、それ以外の場合に 20 のコンピューティング インスタンスと 250 GB のストレージを使用できます。<sup>5</sup>ストレージの制限は、同じ App Service プランのすべてのアプリにまたがる合計コンテンツ サイズです。サポート要求を行って、ストレージの制限を引き上げることもできます。<sup>6</sup>これらのリソースは、専用インスタンス上の物理リソース (インスタンスのサイズとインスタンス数) によって制限されます。<sup>7</sup>Basic レベルでアプリケーションを 2 つのインスタンスにスケーリングする場合、2 つのインスタンスのそれぞれに 350 本の同時接続があります。<sup>8</sup>Premium レベルでは、バックアップの間隔を、App Service 環境の使用時に最小で 5 分ごとまで、それ以外の場合に 1 日 50 回まで下げることができます。<sup>9</sup>カスタムの実行可能ファイルやスクリプトを、オンデマンドやスケジュールで、または App Service インスタンス内のバックグラウンド タスクとして継続的に実行します。Web ジョブを継続的に実行するには、常時接続が必要です。スケジュールされた Web ジョブの実行には、Azure Scheduler Free または Standard が必要です。<sup>10</sup>フェールオーバー用に Azure Traffic Manager が構成された複数のインスタンスを使用するデプロイでは、SLA は 99.95% です。

<!---HONumber=Oct15_HO3-->