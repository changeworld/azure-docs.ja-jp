---
title: Azure Cache for Redis の新機能
description: Azure Cache for Redis の最近の更新
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: reference
ms.date: 10/11/2021
ms.openlocfilehash: c836ed432cb4e138524f3724cb3aa0530039240c
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859363"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Azure Cache for Redis の新機能

## <a name="october-2021"></a>2021 年 10 月

### <a name="azure-cache-for-redis-60-ga"></a>Azure Cache for Redis 6.0 の GA

Azure Cache for Redis 6.0 が一般公開されました。 新しいバージョンには次が含まれます。

- 新しいデータ型である Redis ストリーム
- パフォーマンスの向上
- 開発者の生産性の向上
- セキュリティの強化

追加専用のデータ構造である Redis ストリームを使用して、継続的に生成されるデータを取り込み、管理し、意味を持つものにすることができます。

さらに、Azure Cache for Redis 6.0 には、パフォーマンスと使いやすさのために、新しいコマンドとして `STRALGO`、`ZPOPMIN`、`ZPOPMAX`、`HELP` が導入されています。

今すぐ Azure Cache for Redis 6.0 の使用を開始し、キャッシュ作成時に Redis 6.0 を選択できます。 また、既存の Redis 4.0 キャッシュ インスタンスをアップグレードできます。 詳細については、「[Azure Cache for Redis で Redis のバージョンを設定する](cache-how-to-version.md)」を参照してください。

### <a name="diagnostics-for-connected-clients"></a>接続されているクライアントの診断

Azure Cache for Redis が Azure 診断設定と統合され、キャッシュへのすべてのクライアント接続に関する情報がログに記録されるようになりました。 この診断設定のログ記録と分析は、キャッシュに接続しているユーザーと、それらの接続のタイムスタンプを把握するのに役立ちます。 このデータは、セキュリティ侵害の範囲を特定するため、およびセキュリティ監査の目的に使用できます。 ユーザーは、ストレージ アカウントやイベント ハブなど、選択した宛先にこれらのログをルーティングできます。

詳細については、「[診断設定を使用して Azure Cache for Redis のデータを監視する](cache-monitor-diagnostic-settings.md)」を参照してください。

### <a name="azure-cache-for-redis-enterprise-update"></a>Azure Cache for Redis Enterprise の更新

アクティブ geo レプリケーションのパブリック プレビューで次の機能がサポートされました。

- RediSearch モジュール: アクティブ geo レプリケーションを使用して RediSearch をデプロイする
- 1 つのレプリケーション グループ内に 5 つのキャッシュ。 以前は、2 つのキャッシュがサポートされていました。
- OSS クラスタリング ポリシー - 高パフォーマンスのワークロードに適し、スケーラビリティが向上します。

## <a name="october-2020"></a>2020 年 10 月

### <a name="azure-tls-certificate-change"></a>Azure TLS 証明書の変更

Microsoft では、異なる証明機関 (CA) のセットからの TLS サーバー証明書を使用するように、Azure サービスが更新されています。 この変更は、2020 年 8 月 13 日から 2020 年 10 月 26 日 (推定) のフェーズでロール アウトされます。 Azure では、[現在の CA 証明書が CA/ブラウザー フォーラムのベースライン要件の 1 つにでない](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)ため、この変更が行われます。 この問題は 2020 年 7 月 1 日に報告され、世界中の複数の一般的な公開キー基盤 (PKI) プロバイダーに適用されます。 現在、Azure サービスで使用されている TLS 証明書の多くは、*Baltimore CyberTrust Root* PKI に由来します。 Azure Cache for Redis サービスは、引き続き Baltimore CyberTrust Root に結び付けられます。 これは TLS サーバー証明書ですが、TLS サーバー証明書は、2020 年 10 月 12 日以降、新しい中間証明機関 (ICA) によって発行されます。

> [!NOTE]
> この変更は、パブリック [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)のサービスに限定されます。 ソブリン (中国など) や政府のクラウドは除外されます。
>
>

#### <a name="does-this-change-affect-me"></a>この変更は私に影響しますか?

ほとんどの Azure Cache for Redis のお客様は、この変更の影響を受けないと想定されます。 ご利用のアプリケーションが、受け入れ可能な証明書のリストを明示的に指定する場合 ("証明書のピン留め" と呼ばれる実践方法)、そのアプリケーションは影響を受ける可能性があります。 Baltimore CyberTrust Root の代わりに中間証明書またはリーフ証明書をピン留めしている場合、証明書の構成を変更するには、**直ちに対応する** 必要があります。

次の表に、ロール アウトされる証明書に関する情報を示します。 アプリケーションが使用する証明書によっては、Azure Cache for Redis インスタンスへの接続が失われないように、アプリケーションを更新する必要がある場合があります。

| CA の種類 | Current | ローリング後 (2020 年 10 月 12 日) | アクション |
| ----- | ----- | ----- | ----- |
| Root | 拇印: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 有効期限: 2025 年 5 月 12 日月曜日、午後 4:59:00<br><br> 件名:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | 変更なし | None |
| 中間 | 拇印:<br> CN = Microsoft IT TLS CA 1<br> Thumbprint:417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Thumbprint:54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Thumbprint:8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Thumbprint:Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 有効期限: ‎2024 年 ‎5 月 ‎20 日‎金曜日、午前 5:52:38<br><br> 件名:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | 拇印:<br> CN = Microsoft RSA TLS CA 01<br> Thumbprint:703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 拇印: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 有効期限: ‎2024 年 ‎10 月 ‎8 日火曜日、‎午前 12:00:00;<br><br> 件名:<br> O = Microsoft Corporation<br> C = US<br> | 必須 |

#### <a name="what-actions-should-i-take"></a>どのような対応が必要ですか?

アプリケーションでオペレーティング システムの証明書ストアを使用する場合、またはその他の間で Baltimore ルートをピン留めする場合は、何もする必要はありません。

アプリケーションで中間証明書またはリーフ TLS 証明書をピン留めする場合は、次のルートをピン留めすることをお勧めします。

| Certificate | Thumbprint |
| ----- | ----- |
| [Baltimore Root CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 中間証明書とリーフ証明書の両方が頻繁に変更されることが想定されます。 それらに対して依存関係を使用しないことをお勧めします。 代わりに、アプリケーションをルート証明書にピン留めします。これは、ロール アウトの頻度が低いためです。
>
>

中間証明書を引き続きピン留めするには、ピン留めされた中間証明書リストに次を追加します。これには、将来の変更を最小限に抑えるための追加がいくつか含まれます。

| CA の共通名 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS 発行元 CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS 発行元 CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS 発行元 CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS 発行元 CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

アプリケーションでコード内の証明書が検証される場合は、新しくピン留めされた証明書のプロパティ (発行者、拇印など) を認識するように変更する必要があります。 この追加の検証では、すべてのピン留めされた証明書を今後に対応するように扱う必要があります。

## <a name="next-steps"></a>次の手順

その他の質問がある場合は、[サポート](https://azure.microsoft.com/support/options/)を通じてご連絡ください。  
