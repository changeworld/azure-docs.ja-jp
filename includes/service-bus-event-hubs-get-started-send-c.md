## <a name="send-messages-to-event-hubs"></a>Event Hub へのメッセージ送信
このセクションでは、イベントを Event Hub に送信する C アプリを作成します。 [Apache Qpid プロジェクト](http://qpid.apache.org/)の Proton AMQP ライブラリを使用します。 これは、 [ここ](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)に示すように、C の AMQP を Service Bus キューと Topics と使用するのに似ています。 詳細については、「 [Qpid Proton のドキュメント](http://qpid.apache.org/proton/index.html)」を参照してください。

1. [Qpid AMQP Messenger ページ](http://qpid.apache.org/components/messenger/index.html)の **[Installing Qpid Proton]** リンクをクリックして、環境に応じた指示に従ってください。 ここでは、Linux 環境で [Azure Linux VM](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) と Ubuntu 14.04 を使用していると仮定します。
2. Proton ライブラリをコンパイルするには、次のパッケージをインストールします。
   
    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. [Qpid Proton ライブラリ](http://qpid.apache.org/proton/index.html) をダウンロードし、次のように抽出します。
   
    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. build ディレクトリを作成し、コンパイルとインストールを行います。
   
    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. 作業ディレクトリに **sender.c** と呼ばれる新しいファイルを次の内容で作成します。 Event Hub の名前と名前空間の名前の値を置き換えます (通常、後者は `{event hub name}-ns`です)。 前に作成した **SendRule** のキーの URL でエンコードされたバージョンも代入する必要があります。 [ここ](http://www.w3schools.com/tags/ref_urlencode.asp)で URL でエンコードすることができます。
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. **gcc**でファイルをコンパイルします。
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [!NOTE]
> コンパイルしたコードで、1 の送信ウィンドウを使用して、メッセージをできるだけ早く強制的に送信します。 一般に、アプリケーションではスループットが向上するようにメッセージをバッチ処理する必要があります。 この環境やその他の環境、バインドが提供されているプラットフォーム (現在は、Perl、PHP、Python、Ruby) から Qpid Proton ライブラリを使用する方法の詳細については、「 [Qpid AMQP Messenger ページ](http://qpid.apache.org/components/messenger/index.html) 」を参照してください。
> 
> 



<!--HONumber=Nov16_HO3-->


