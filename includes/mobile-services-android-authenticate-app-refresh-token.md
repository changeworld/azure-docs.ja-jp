簡単なケースでは、トークン キャッシュは動作しますが、トークンが期限切れになった場合、または取り消された場合はどのようになるでしょうか。期限切れのトークンは、検出できるようにする必要があります。トークンが期限切れになる可能性があるのは、アプリケーションが実行されていないときです。つまり、このときにトークン キャッシュは無効になることがあります。さらに、アプリケーションによる直接の呼び出し中、または Mobile Services ライブラリによる呼び出し中に、アプリケーションが実際に実行されていると、期限切れになる場合もあります。その結果、HTTP ステータス コード 401 "認証エラー" が発生します。よって、この状態を検出して、トークンを更新する必要があります。そのためには、[Android クライアント ライブラリ][Android クライアント ライブラリ]の [ServiceFilter][ServiceFilter] を使用します。

このセクションでは、HTTP ステータス コード 401 の応答を検出し、トークンとトークン キャッシュの更新を開始するように ServiceFilter を定義します。さらに、この ServiceFilter では認証中に他の送信要求をブロックして、これらの要求が更新されたトークンを使用できるようにします。

1.  Eclipse で、ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

        import java.util.concurrent.atomic.AtomicBoolean;

2.  ToDoActivity.java ファイル内で、次のメンバーを ToDoActivity クラスに追加します。

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    これらは、ユーザーの認証を同期しやすくするために使用します。認証が必要なのは、一度だけです。認証中の呼び出しがすべて待機し、進行中の認証から新しいトークンを取得して使用する必要があります。

3.  ToDoActivity.java ファイルで、次のメソッドを ToDoActivity クラスに追加します。このメソッドは、認証の進行中に他のスレッドでの送信呼び出しをブロックするために使用します。

        /**
         * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
         */
        public boolean detectAndWaitForAuthentication()
        {
            boolean detected = false;
            synchronized(mAuthenticationLock)
            {
                do
                {
                    if (bAuthenticating == true)
                        detected = true;
                    try
                    {
                        mAuthenticationLock.wait(1000);
                    }
                    catch(InterruptedException e)
                    {}
                }
                while(bAuthenticating == true);
            }
            if (bAuthenticating == true)
                return true;

            return detected;
        }

4.  ToDoActivity.java ファイル内で、次のメソッドを ToDoActivity クラスに追加します。このメソッドは、実際に待機を開始し、認証の完了時に送信呼び出しでトークンを更新します。

        /**
         * Waits for authentication to complete then adds or updates the token 
         * in the X-ZUMO-AUTH request header.
         * 
         * @param request
         *            The request that receives the updated token.
         */
        private void waitAndUpdateRequestToken(ServiceFilterRequest request)
        {
            MobileServiceUser user = null;
            if (detectAndWaitForAuthentication())
            {
                user = mClient.getCurrentUser();
                if (user != null)
                {
                    request.removeHeader("X-ZUMO-AUTH");
                    request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
                }
            }
        }

5.  ToDoActivity.java ファイルで、`authenticate` メソッドを更新し、トークンとトークン キャッシュを強制的に更新するブール型パラメーターを受け入れるようにします。また、ブロックされたスレッドが新しいトークンを取得できるように、認証が完了したらこれらにスレッドに通知する必要があります。

        /**
         * Authenticates with the desired login provider. Also caches the token. 
         * 
         * If a local token cache is detected, the token cache is used instead of an actual 
         * login unless bRefresh is set to true forcing a refresh.
         * 
         * @param bRefreshCache
         *            Indicates whether to force a token refresh. 
         */
        private void authenticate(boolean bRefreshCache) {

            bAuthenticating = true;

            if (bRefreshCache || !loadUserTokenCache(mClient))
            {
                // New login using the provider and update the token cache.
                mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                        new UserAuthenticationCallback() {
                            @Override
                            public void onCompleted(MobileServiceUser user,
                                    Exception exception, ServiceFilterResponse response) {

                                synchronized(mAuthenticationLock)
                                {
                                    if (exception == null) {
                                        cacheUserToken(mClient.getCurrentUser());
                                        createTable();
                                    } else {
                                        createAndShowDialog(exception.getMessage(), "Login Error");
                                    }
                                    bAuthenticating = false;
                                    mAuthenticationLock.notifyAll();
                                }
                            }
                        });
            }
            else
            {
                // Other threads may be blocked waiting to be notified when 
                // authentication is complete.
                synchronized(mAuthenticationLock)
                {
                    bAuthenticating = false;
                    mAuthenticationLock.notifyAll();
                }
                createTable();
            }
        }   

6.  ToDoActivity.java ファイル内で、ToDoActivity クラスの `RefreshTokenCacheFilter` クラスに対して、次の定義を追加します。

        /**
         * The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
         * When 401 is encountered, the filter calls the authenticate method on the 
         * UI thread. Out going requests and retries are blocked during authentication. 
         * Once authentication is complete, the token cache is updated and 
         * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
         * that request.   
         */
        private class RefreshTokenCacheFilter implements ServiceFilter {

            AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();

            /**
             * The AuthenticationRetryFilterCallback class is a wrapper around the response 
             * callback that encapsulates the request and other information needed to enable 
             * a retry of the request when HTTP status code 401 is encountered. 
             */
            private class AuthenticationRetryFilterCallback implements ServiceFilterResponseCallback
            {
                // Data members used to retry the request during the response.
                ServiceFilterRequest mRequest;
                NextServiceFilterCallback mNextServiceFilterCallback;
                ServiceFilterResponseCallback mResponseCallback;

                public AuthenticationRetryFilterCallback(ServiceFilterRequest request, 
                        NextServiceFilterCallback nextServiceFilterCallback, 
                        ServiceFilterResponseCallback responseCallback)
                {
                    mRequest = request;
                    mNextServiceFilterCallback = nextServiceFilterCallback;
                    mResponseCallback = responseCallback;
                }

                @Override
                public void onResponse(ServiceFilterResponse response, Exception exception) {

                    // Filter out the 401 responses to update the token cache and 
                    // retry the request
                    if ((response != null) && (response.getStatus().getStatusCode() == 401))
                    { 
                        // Two simultaneous requests from independent threads could get HTTP 
                        // status 401. Protecting against that right here so multiple 
                        // authentication requests are not setup to run on the UI thread.
                        // We only want to authenticate once. Other requests should just wait 
                        // and retry with the new token.
                        if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                           
                        {
                            // Authenticate on UI thread
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    // Force a token refresh during authentication.
                                    authenticate(true);
                                }
                            });
                        }

                        // Wait for authentication to complete then 
                        // update the token in the request.
                        waitAndUpdateRequestToken(this.mRequest);
                        mAtomicAuthenticatingFlag.set(false);                       

                        // Retry recursively with a new token as long as we get a 401.
                        mNextServiceFilterCallback.onNext(this.mRequest, this);
                    }

                    // Responses that do not have 401 status codes just pass through.
                    else if (this.mResponseCallback != null)  
                      mResponseCallback.onResponse(response, exception);
                }
            }


            @Override
            public void handleRequest(final ServiceFilterRequest request, 
                final NextServiceFilterCallback nextServiceFilterCallback,
                ServiceFilterResponseCallback responseCallback) {

                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);

                // Wrap the request in a callback object that will facilitate retries.
                AuthenticationRetryFilterCallback retryCallbackObject = 
                    new AuthenticationRetryFilterCallback(request, nextServiceFilterCallback,
                          responseCallback); 

                // Send the request down the filter chain.
                nextServiceFilterCallback.onNext(request, retryCallbackObject);         
            }
        }

    このサービス フィルターでは、各応答で HTTP ステータス コード 401 "認証エラー" が発生していないかどうかを確認します。401 が発生した場合は、新しいトークンを取得する新しいログイン要求が、UI スレッドで設定されます。その他の呼び出しは、ログインが完了するまでブロックされます。新しいトークンが取得されると、401 を引き起こした要求は新しいトークンを使用して再試行されます。またブロックされた呼び出しも、新しいトークンを使用して再試行されます。

7.  次のように、ToDoActivity.java ファイルで、`onCreate` メソッドを更新します。

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            setContentView(R.layout.activity_to_do);
            mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);

            // Initialize the progress bar
            mProgressBar.setVisibility(ProgressBar.GONE);

            try {
                // Create the Mobile Service Client instance, using the provided
                // Mobile Service URL and key
                mClient = new MobileServiceClient(
                        "https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
                        "<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());

                // Authenticate passing false to load the current token cache if available.
                authenticate(false);

            } catch (MalformedURLException e) {
                createAndShowDialog(new Exception("Error creating the Mobile Service. " +
                    "Verify the URL"), "Error");
            }
        }

    このコードでは、ProgressFilter 以外に RefreshTokenCacheFilter が使用されています。また、`onCreate` の間に、トークン キャッシュも読み込みます。そのため、false が `authenticate` メソッドに渡されます。

  [Android クライアント ライブラリ]: http://dl.windowsazure.com/androiddocs/
  [ServiceFilter]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html
