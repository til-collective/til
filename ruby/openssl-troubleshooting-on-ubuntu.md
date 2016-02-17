# OpenSSL SSLv3_client troubleshooting on Ubuntu 14.04

After an entire week looking for tutorials and many attempts, here is how I managed to fix the OpenSSL SSLv3_client issue on Linux, found when trying to install Ruby >= 2.1.* through RVM.

1. Installed the openssl dependency for Ruby:

    ```
    rvm pkg install openssl
    ```

2. So then I could finally install Ruby 2.2.3:

    ```
    rvm install 2.2.3 --with-openssl-dir=$HOME/.rvm/usr
    rvm list
    rvm --default use 2.2.3
    ```

