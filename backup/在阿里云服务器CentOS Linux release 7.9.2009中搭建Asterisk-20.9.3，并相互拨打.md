1.  更新或安装软件包  
   
    `yum install -y epel-release dmidecode gcc-c++ ncurses-devel libxml2-devel make wget openssl-devel newt-devel kernel-devel sqlite-devel libuuid-devel gtk2-devel jansson-devel binutils-devel libedit libedit-devel`  

2. 一步一步执行下面命令  
    `cd /usr/src`  

    `wget https://downloads.asterisk.org/pub/telephony/asterisk/asterisk-20-current.tar.gz`  

    `tar -zxvf asterisk-20-current.tar.gz`  

    `cd asterisk-20.9.3`  

    `sudo contrib/scripts/install_prereq install`  

    `./configure --libdir=/usr/lib64 --with-jansson-bundled`  

    `make menuselect && make && sudo make install && make samples && make config`  

    `service asterisk start`  

    `asterisk -rvv`  

 > [!NOTE]  
    1. `./configure --libdir=/usr/lib64 --with-jansson-bundled     (apartir de asterisk 16: --with-jansson-bundled).`  
    2. asterisk-20-current.tar.gz下载很慢，本地下载然后FTP上传  
    3. 在configure,也需要下载文件，但是直接下载会慢，所以本地下载然后FTP上传

3. 开始配置asterisk  

    `cd /etc/asterisk`  

    `mv sip.conf.sample sip.conf`  

    ```conf
    [general]
    context=default
    nat=force_rport,comedia
    videosupport=yes
    extern_ip=x.x.x. ; 你的公共 IP 地址
    local_net=192.168.1.0/255.255.255.0 ; 你的本地网络（例如，192.168.1.0/255.255.255.0）

    [6001]
    type=user
    context=from-internal
    host=dynamic
    secret=6001
    disallow=all
    allow=gsm
    allow=ulaw

    [6002]
    type=user
    context=from-internal
    host=dynamic
    secret=6002
    disallow=all
    allow=gsm
    allow=ulaw
    ```  

    `mv extensions.conf.sample extensions.conf`  

    ```conf
    [from-internal]
    exten = 6001,1,Dial(PJSIP/6001,60)
    exten = 6002,1,Dial(PJSIP/6002,60)

    exten = 6001,1,Dial(SIP/6001,60)
    exten = 6002,1,Dial(SIP/6002,60)
    ```  

    `mv pjsip.conf.sample pjsip.conf`  

    ```conf
    [port-udp]
    type=transport
    protocol=udp
    bind=0.0.0.0:5060
    external_media_address=x.x.x.x ; 你的公共 IP 地址
    external_signaling_address=x.x.x.x ; 你的公共 IP 地址

    [general]
    extern_ip=x.x.x.x         ; 你的公共 IP 地址
    local_net=192.168.1.0/255.255.255.0  ; 你的本地网络（例如，192.168.1.0/255.255.255.0）

    ; 设置 NAT 选项
    nat=force_rport,comedia
    videosupport=yes

    [6001]
    type=endpoint
    context=from-internal
    disallow=all
    allow=gsm
    allow=ulaw
    auth=6001
    aors=6001

    [6001]
    type=auth
    auth_type=userpass
    password=userpass
    username=6001

    [6001]
    type=aor
    max_contacts=10

    [6002]
    type=endpoint
    context=from-internal
    disallow=all
    allow=gsm
    allow=ulaw
    auth=6002
    aors=6002

    [6002]
    type=auth
    auth_type=userpass
    password=userpass
    username=6002

    [6002]
    type=aor
    max_contacts=10
    ```

> [!WARNING]
     1. 服务端和客户端allow=gsm要一致  

