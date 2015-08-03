<properties
   pageTitle="NuGet パッケージ | Microsoft Azure"
   description="一般的な再試行ポリシーの作業用の NuGet パッケージに関するガイダンスです。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# NuGet パッケージ

<p class="lead">通信を開始するコンポーネントが増えるにつれて、一時的なエラーをうまく処理することがより重要になります。一時的な障害処理動作は、再試行ポリシーによって処理されます。NuGet パッケージは、1 つのインスタンス内で複数の再試行を処理するのに役立ちます。</p>

> このドキュメントは、概念実証としてドラフトに基づいて作成されました。実際に確認されたガイダンスではありません。

Patterns & Practices の `TransientFaultHandling` コードは、一般的な再試行ポリシーの動作に推奨されています。

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## 構成

セクションには、再試行機能の構成情報が含まれています。

パラメーター | 説明
-------------------- | ----------------------
MaximumExecutionTime | 要求の最大実行時間 (考えられるすべての再試行が含まれます)。
ServerTimeOut | 要求のサーバー タイムアウト間隔
RetryPolicy | 再試行ポリシー。以下の「ポリシー」セクションを参照してください。

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

プログラム：

- クライアント側での設定のサポート。
- クライアントが指定した操作におけるオーバーライドの有効化。

構成ファイル:

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## ポリシー

### 指数

サービスの調整を回避するために、サービスの呼び出しを指数関数的に繰り返し試行するように配置する場合に使用されます。

__方法:__

2 回目以降の試行のバックオフ間隔を指数関数的に大きくします。すべてのクライアントが同時に再試行しないように、-20% ～ +20% の無作為化をバックオフ間隔に追加します。

__構成:__

パラメーター | 説明
-------------------- | -------------------------------------------------------
maxAttempt | 再試行回数。
deltaBackoff | 再試行のバックオフ間隔。この期間の倍数は、後続の再試行に使用されます。
MinBackoff | deltaBackoff から計算されたすべての再試行間隔に追加されます。
FastFirst | 最初の即時再試行
MaxBackoff | MaxBackoff は、計算された再試行間隔が MaxBackoff より大きい場合に使用されます。この値は変更できません。

__実装ロジック:__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## 線形

サービスの調整を回避するために、サービスの呼び出しを直線的に繰り返し試行するように配置する場合に使用されます。

__方法:__

試行間隔に指定された固定間隔を使用して、指定された回数の試行を実行します。すべてのクライアントが同時に再試行しないように、-20% ～ +20% の無作為化をバックオフ間隔に追加します。

__構成:__

パラメーター | 説明
-------------------- | -------------------------------------------------------
maxAttempt | 再試行回数。
deltaBackoff | 再試行のバックオフ間隔。
FastFirst | 最初の即時再試行

__実装ロジック:__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## アダプティブ

応答ヘッダーのサービスによって渡されたエラー コード/メタデータに基づいてサービスの呼び出しを繰り返し試行するように配置する場合に使用されます。

__方法:__

応答ヘッダーのサービスによって渡されたエラー コード/メタデータに基づいて計算されたバックオフ間隔を使用して、指定された回数の再試行を実行します。


__構成:__

構成できません。

__実装ロジック:__

応答ヘッダーのサービスによって渡されたエラー コード/メタデータに基づきます。

__サーキット ブレーカー:__

[Circuit Breaker](http://msdn.microsoft.com/library/dn589784.aspx) に基づいています。

## 機能拡張

カスタムの再試行ポリシーを提供するために実装できるパブリック インターフェイス

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## テレメトリ

EventSource を使って再試行を ETW イベントとしてログに記録します。ここでは、再試行するたびにログに記録する必要があるフィールドを示します。

パラメーター | 説明
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operation | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "9/5/2014 10:00:13 PM"
operationEndTime | "9/5/2014 10:00:14 PM"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=July15_HO4-->