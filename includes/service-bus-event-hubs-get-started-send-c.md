## <a name="send-messages-to-event-hubs"></a>Envoyer des messages à des concentrateurs d’événement

Dans cette section, nous développerons une application C pour envoyer des événements à votre événement de supervision. Nous allons utiliser la bibliothèque AMQP de PROTONS à partir du [projet de Apache Qpid](http://qpid.apache.org/). Ceci est analogue à l’utilisation de files d’attente de Bus de services et rubriques avec AMQP de C comme indiqué [ici](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Pour plus d’informations, consultez la [documentation de PROTONS de Qpid](http://qpid.apache.org/proton/index.html).

1. À partir de la [page Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), cliquez sur le lien **Installation de PROTONS de Qpid** et suivez les instructions en fonction de votre environnement. Nous supposons un environnement Linux. par exemple, une [Machine virtuelle Linux de Azure](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) avec 14.04 d’Ubuntu.

2. Pour compiler la bibliothèque de PROTONS, installez les packages suivants :

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Télécharger la bibliothèque [Qpid PROTONS](http://qpid.apache.org/proton/index.html) et l’extraire, par exemple :

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Créer un répertoire de build, la compilation et l’installation :

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. Dans votre répertoire de travail, créez un nouveau fichier nommé **sender.c** avec le contenu suivant. N’oubliez pas de remplacer la valeur de votre nom de concentrateur d’événements et le nom de l’espace de noms (cette dernière est généralement `{event hub name}-ns`). Vous devez également remplacer une version codée en URL de la clé pour le **SendRule** créé précédemment. Vous pouvez coder en URL il [ici](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
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

6. Compilez le fichier, en supposant que de **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] Dans ce code, nous utilisons une fenêtre sortante de 1 pour forcer les messages sortants dès que possible. En général, votre application doit tenter de messages de traitement par lots pour augmenter le débit. Consultez la [page Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) pour plus d’informations sur l’utilisation de la bibliothèque Qpid PROTONS dans ce guide et autres environnements et à partir de plates-formes pour lesquels les liaisons sont fournis (actuellement Perl, PHP, Python et Ruby).
