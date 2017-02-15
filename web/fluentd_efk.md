(Elasticsearch+Fluentd+Kibana) 記錄：

#for mac: (http://docs.fluentd.org/v0.12/articles/free-alternative-to-splunk-by-fluentd)
##先建立一個資料夾來放檔案
* $ mkdir fluentd
* $ cd ./fluentd

##確認 java版本 （Java for Elasticsearch, Java version is 8 or higher.）, 如果不是，就安裝信8以上的版本
* $ java -version
    java version "1.8.0_111"
    Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
    Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)

##安裝 Elasticsearch
* $ curl -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.0.2.tar.gz
* $ tar zxvf elasticsearch-5.0.2.tar.gz
* $ cd elasticsearch-5.0.2
安裝完畢後執行
* $ ./bin/elasticsearch

##安裝 Kibana
* $ curl -O https://artifacts.elastic.co/downloads/kibana/kibana-5.0.2-darwin-x86_64.tar.gz
* $ tar zxvf kibana-5.0.2-darwin-x86_64.tar.gz
* $ cd kibana-5.0.2-darwin-x86_64
安裝完畢後執行
* $ ./bin/kibana
可開啟 http://localhost:5601 查看 Kibana 頁面

##安裝 Fluentd(td-agent)
有 多種下載方式，查看官方說明。(http://www.fluentd.org/download)
以 gem 安裝為例 (http://docs.fluentd.org/v0.12/articles/install-by-gem)

###安裝
* $ gem install fluentd -v "~> 0.12.0" --no-ri --no-rdoc
安裝完成後,建立config設定檔
* $ fluentd --setup ./fluent
# 執行
* $ fluentd -c ./fluent/fluent.conf
# 測試是否成功紀錄
* $ echo '{"json":"message"}' | fluent-cat debug.test

##連結 fluentd & elasticsearch
* $ fluent-gem install fluent-plugin-elasticsearch --no-document

###修改 fluent/fluent.con 如下
（設定檔可參考 http://docs.fluentd.org/v0.12/articles/config-file）

    ## built-in TCP input
    ## $ echo <json> | fluent-cat <tag>
    <source>
      @type forward
      @id forward_input
    </source>
    ## match tag=debug.** and dump to console
    <match debug.**>
      @type stdout
      @id stdout_output
    </match>
    # elasticsearch
    <match fluentd.test.**>
      @type copy
      <store>
        @type elasticsearch
        logstash_format true
        flush_interval 10s
      </store>
      <store>
        @type file
        path ./product.log
      </store>
    </match>

修改完成後，重啟 fluentd 即可。



#for ubuntu 16.04 (use nginx)

##確認 java版本 （Java for Elasticsearch, Java version is 8 or higher.）
安裝 java 8
* $ sudo apt-get -y install oracle-java8-installer

##deb 安裝 fluentd (http://docs.fluentd.org/v0.12/articles/install-by-deb)
* $ curl -L https://toolbelt.treasuredata.com/sh/install-ubuntu-xenial-td-agent2.sh | sh
開啟
* $ sudo /etc/init.d/td-agent start

##deb 安裝 elasticsearch
* $ curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
* $ sudo dpkg -i elasticsearch-5.2.0.deb
* $ sudo /etc/init.d/elasticsearch start

##deb 安裝 kibana
(參考 https://www.elastic.co/guide/en/kibana/current/deb.html)
* $ curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-amd64.deb
* $ sudo dpkg -i kibana-5.2.0-amd64.deb
* $ sudo /etc/init.d/kibana start

##連結 fluentd & elasticsearch
* $ sudo /usr/sbin/td-agent-gem install fluent-plugin-elasticsearch --no-document

###fluentd 設定檔
* $ sudo nano /etc/td-agent/td-agent.conf

    <match debug.**>
      @type stdout
    </match>
    <source>
      @type forward
      @id forward_input
    </source>
    <match fluentd.test.**>
      @type copy
      <store>
        @type elasticsearch
        logstash_format true
        flush_interval 10s
      </store>
      <store>
        @type file
        path /var/log/td-agent/td-%Y-%m-%d/%H.log
      </store>
    </match>

##修改 nginx 設定檔(參考 https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elk-stack-on-ubuntu-16-04)
    server {
      listen 80;
      server_name example.com;


      auth_basic "Restricted Access";
      auth_basic_user_file /etc/nginx/htpasswd.users;

      location / {
        proxy_pass http://localhost:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
      }
    }
* $ sudo nginx service restart

可開啟網頁查看資料了。


＊使用 ruby & rails 寫入資料 到 fluentd
Gemfile 增加
`gem 'fluent-logger', "~> 0.4.3"`

執行寫入：
    Fluent::Logger::FluentLogger.open(nil, :host=>'localhost', :port=>24224)
    Fluent::Logger.post("fluentd.test.follow", {"from"=>"mingyuok!!212", "to"=>"userB"})


## nginx 驗證帳密(設定)
    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/.htpasswd;

帳密設定檔儲存於 /etc/nginx/.htpasswd
建立帳號密碼指令為：
* $ sudo sh -c "echo -n 'USERNAME:' >> /etc/nginx/.htpasswd" #建立帳號。USERNAME 取代為使用者帳號。
* $ sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd" #前述建立的帳號建立密碼。輸入指令後，會跳出輸入框輸入密碼。



後記：
elasticsearch好像會需要大量記憶體。
http://suitingtseng.logdown.com/posts/2015/08/15/elasticsearch