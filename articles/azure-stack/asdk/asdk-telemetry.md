---
title: Azure Stack のテレメトリ | Microsoft Docs
description: Azure Stack のテレメトリの設定を PowerShell を使用して構成する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bfd16901c5ce036719a1ed19e9a5b5c6ef52be93
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257426"
---
# <a name="azure-stack-telemetry"></a>Azure Stack のテレメトリ

Azure Stack システムのデータ (テレメトリ) は、"接続ユーザー エクスペリエンス" を介して自動的に Microsoft にアップロードされます。 Azure Stack のテレメトリから収集されたデータは、カスタマー エクスペリエンスを高めること、またセキュリティや正常性、品質、パフォーマンスを分析することを主な目的として Microsoft のチームが使用します。

Azure Stack のオペレーターにとってテレメトリは、企業でのデプロイに関する重要な分析情報であり、今後登場する Azure Stack の新バージョンを形作る声でもあります。

> [!NOTE]
> Azure Stack は、課金のための使用状況情報を Azure に転送するように構成することもできます。 マルチノードの Azure Stack を利用しており、従量課金制を選択した場合は、この構成が必須となります。 使用状況のレポートは、テレメトリとは別に管理されます。マルチノードをご利用の場合でも、容量モデルを選択したユーザーや Azure Stack Development Kit ユーザーについては、使用状況の報告は不要です。 そのような場合は、使用状況のレポートを[登録スクリプトを使って](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting)無効にすることができます。

Azure Stack テレメトリの基盤になっているのは、Windows Server 2016 の "接続ユーザー エクスペリエンスとテレメトリ" コンポーネントです。このコンポーネントでは、テレメトリのイベントやデータが、[Windows イベント トレーシング (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) のトレース ログ テクノロジを使って収集、保存されます。 Azure Stack のコンポーネントにも、同じログ テクノロジが使用されており、パブリックなオペレーティング システムのイベント ログとトレース API を使って収集されたイベントとデータが発行されます。 Azure Stack のコンポーネントの例として、ネットワーク リソース プロバイダー、ストレージ リソース プロバイダー、監視リソース プロバイダー、更新リソース プロバイダーがあります。 "接続ユーザー エクスペリエンスとテレメトリ" コンポーネントは、SSL を使用してデータを暗号化し、証明書のピン留めを使用してテレメトリ データを HTTPS で Microsoft Data Management サービスに送信します。

> [!NOTE]
> テレメトリ データ フローをサポートするには、ネットワークでポート 443 (HTTPS) を開く必要があります。 "接続ユーザー エクスペリエンスとテレメトリ" コンポーネントから Microsoft Data Management サービスへの接続には、https://v10.vortex-win.data.microsoft.com が使われます。また、"接続ユーザー エクスペリエンスとテレメトリ" コンポーネントは、構成情報をダウンロードする目的で https://settings-win.data.microsoft.com にも接続します。

## <a name="privacy-considerations"></a>プライバシーに関する考慮事項
テレメトリ データは、ETW サービスによって、保護されたクラウド ストレージに戻されます。 テレメトリ データへのアクセスには、最小特権の原則が適用されます。 テレメトリ データへのアクセスは、業務上の正当な必要性がある Microsoft 担当者にしか認められません。 お客様の判断による場合や、[Azure Stack のプライバシーに関する声明](https://privacy.microsoft.com/PrivacyStatement)に記載されている限定的な目的を除き、Microsoft がお客様の個人データを第三者と共有することはありません。 ただし Microsoft は、集計されて匿名化されたテレメトリ情報を含んだビジネス レポートについては、OEM やパートナーと共有します。 データ共有の決定は、プライバシー、法務、データ管理の関係者を含む Microsoft 社内のチームが行います。

Microsoft は、情報量の最小化に賛同し、実践しています。 必要な情報だけを収集するよう努め、サービスまたは分析に必要な期間のみ保存しています。 Azure Stack システムや Azure サービスがどのように機能しているかについての情報は、その大半が 6 か月以内に削除されます。 要約されたデータや集計されたデータの保存期間は、それよりも長くなります。

お客様の情報のプライバシーとセキュリティが重要であることを Microsoft は理解しています。 Azure Stack では、お客様のプライバシーとデータ保護に関して、熟慮を重ねながら包括的な取り組みを行ってきました。 各種機能やプライバシー設定は、IT 管理者の権限でいつでもカスタマイズできます。 透明性と信用を確保するために、Microsoft は以下のことを表明しています。
- 収集するデータの種類をありのままお客様に伝えます。
- 企業ユーザーによる独自管理を可能にします。お客様は自社のプライバシー設定を独自にカスタマイズすることができます。
- お客様のプライバシーとセキュリティを最優先事項とします。
- テレメトリの用途について透明性を確保します。
- テレメトリは、カスタマー エクスペリエンスの向上を目的として使用されます。

Microsoft が意図して機密情報 (クレジット カード番号、ユーザー名とパスワード、メール アドレスなど、機密性の高い情報) を収集することはありません。 意図せず機密情報を受け取ったと Microsoft が判断した場合には、削除します。

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft によるテレメトリ データの使用例
テレメトリは、信頼性にかかわる重大な問題を Microsoft がお客様のデプロイと構成からいち早く特定し、修正するうえで重要な役割を果たします。 テレメトリ データの収集を通じて得られる分析情報は、サービスの問題やハードウェア構成の問題を速やかに把握するために役立てられます。 Microsoft は、こうしたデータをお客様から収集し、エコシステム向上の原動力とすることができるからこそ、その統合された Azure Stack ソリューションにより高い基準を設定することができるのです。

テレメトリは、お客様がどのようにコンポーネントをデプロイし、各種機能を使用しているか、またお客様がどのようにサービスを使用してそのビジネス目標を達成しているかについて、Microsoft が理解を深めるうえでも役立ちます。 そうしたデータから洞察を得ることによって、お客様のエクスペリエンスやワークロードに直接影響する領域に対して優先的にエンジニアリングの投資を行うことができます。

たとえば、Azure Stack ロールに関連付けられているコンテナー、ストレージ、ネットワーク構成が、お客様によってどのように使用されているか、といった情報が挙げられます。 そのような分析情報も、Microsoft の管理ソリューションと監視ソリューションの改善やインテリジェンスに活かされます。 その結果、お客様は品質の問題を診断しやすくなり、また、Microsoft サポートへの問い合わせの件数が減ることでコストの削減にもつながります。

## <a name="manage-telemetry-collection"></a>テレメトリの収集を管理する
テレメトリは製品の機能強化や安定性の向上に寄与するデータであるため、組織でテレメトリを無効にすることはお勧めできません。 ただし、それが必要になる状況も、ときにはあることを十分に理解しています。

そのような場合は、Microsoft に送信するテレメトリのレベルを構成することができます。これは、デプロイ前はレジストリ設定を、デプロイ後はテレメトリ エンドポイントを使用して行います。

### <a name="set-telemetry-level-in-the-windows-registry"></a>Windows レジストリでテレメトリ レベルを設定する
Azure Stack をデプロイする前に、物理ホスト コンピューターに対してテレメトリ レベルを手動で設定するには、Windows レジストリ エディターを使用します。 グループ ポリシーなどの管理ポリシーが既に存在する場合、そのポリシーがこのレジストリ設定よりも優先されます。

開発キット ホストに Azure Stack をデプロイする前に、CloudBuilder.vhdx で起動し、管理者特権の PowerShell ウィンドウで次のスクリプトを実行します。

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

テレメトリ レベルは累積形式になっており、4 つのレベル (0 ～ 3) に分類されます。

**0 (Security)**。 セキュリティ データのみ。 "接続ユーザー エクスペリエンスとテレメトリ" コンポーネントの設定に関するデータや Windows Defender に関するデータなど、オペレーティング システムのセキュリティを保つために必要な情報が対象となります。 Azure Stack に固有のテレメトリは、このレベルでは出力されません。

**1 (Basic)**。 Security データに加え、基本的な正常性データと品質データ。 Security レベルからのデータに加え、品質関連のデータ、アプリの互換性、アプリの利用状況データなど、基本的なデバイス情報が対象となります。 テレメトリ レベルを Basic に設定すると、Azure Stack のテレメトリが有効になります。 このレベルで収集されるデータの例を次に示します。

- **基本的なデバイス情報**: エコシステムに存在する Windows Server 2016 のネイティブ インスタンスと仮想化インスタンスの構成や種類が明らかとなります。具体的には、次のような情報が収集されます。
 - マシン属性 (OEM、モデルなど)
 - ネットワーク属性 (ネットワーク アダプターの数や速度など)
 - プロセッサとメモリの属性 (コア数、メモリ サイズなど)
 - ストレージ属性 (ドライブの数、種類、サイズなど)
- **テレメトリの機能**: アップロードされたイベントの割合、ドロップされたイベントの割合、前回のアップロード時刻などが収集されます。
- **品質関連の情報**: Microsoft は、この情報を通じて、Azure Stack がどのように実行されているかについての基本的な理解を深めることができます。 たとえば、特定のハードウェア構成に関する重要なアラートの件数が該当します。
- \*\*互換性データ: システムや仮想マシンにインストールされているリソース プロバイダーを把握したり、互換性に関する問題のリスクを特定したりするために役立てられます。

**2 (Enhanced)**。 さらに詳しい分析情報につながるデータ。Basic レベルと Security レベルからのデータに加え、オペレーティング システムや他の Azure Stack サービスがどのように使用され、どのように実行されているかについてのデータ、詳細な信頼性データが対象となります。

**3 (Full)**。 **Security**、**Basic**、**Enhanced** の各レベルからのデータに加え、問題を特定して修正するために必要なあらゆるデータ。

> [!NOTE]
> 既定のテレメトリ レベル値は 2 (Enhanced) です。

Windows と Azure Stack のテレメトリをオフにすると、SQL のテレメトリも無効になります。 Windows Server のテレメトリ設定の作用について詳しくは、[Windows テレメトリのホワイトペーパー](https://aka.ms/winservtelemetry)をご覧ください。

> [!IMPORTANT]
> これらのテレメトリ レベルが適用されるのは、Microsoft Azure Stack のコンポーネントだけです。 Azure Stack ハードウェア パートナーのハードウェア ライフサイクル ホストで動作する、Microsoft 以外のソフトウェア コンポーネントやサービスは、前述したどのレベルにも該当しないテレメトリ レベルで、そのクラウド サービスと通信を行うことが考えられます。 そのテレメトリ ポリシーの具体的な内容と、オプトイン/オプトアウトの方法については、ご利用の Azure Stack ハードウェア ソリューション プロバイダーにお問い合わせください。

### <a name="enable-or-disable-telemetry-after-deployment"></a>デプロイ後にテレメトリを有効/無効にする

デプロイ後にテレメトリを有効または無効にするには、ERCS VM に公開されている PEP (Privileged End Point: 特権エンドポイント) にアクセスできることが必要です。
1.  有効にする場合: `Set-Telemetry -Enable`
2.  無効にする場合: `Set-Telemetry -Disable`

パラメーターの詳細:
> .PARAMETER Enable - テレメトリ データのアップロードを有効にします。

> .PARAMETER Disable - テレメトリ データのアップロードを無効にします。  

**テレメトリを有効にするためのスクリプト:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**テレメトリを無効にするためのスクリプト:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>次の手順
[マーケットプレース項目の追加](asdk-marketplace-item.md)
