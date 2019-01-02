ClickHouse 部署


https://packagecloud.io/Altinity/clickhouse/ 
https://packagecloud.io/Altinity/clickhouse?page=9

https://blog.csdn.net/qq_18931433/article/details/82494182


1.   下载 rpm 包：
wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-server-common-1.1.54383-1.el7.x86_64.rpm
wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-common-static-1.1.54383-1.el7.x86_64.rpm
wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-server-1.1.54383-1.el7.x86_64.rpm
wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-debuginfo-1.1.54383-1.el7.x86_64.rpm
wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-test-1.1.54383-1.el7.x86_64.rpm
wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-client-1.1.54383-1.el7.x86_64.rpm

2.    顺序安装
curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash   --初始化环境

sudo yum install clickhouse-server-common-1.1.54383-1.el7.x86_64
sudo yum install clickhouse-common-static-1.1.54383-1.el7.x86_64
sudo yum install clickhouse-server-1.1.54383-1.el7.x86_64
sudo yum install clickhouse-debuginfo-1.1.54383-1.el7.x86_64
sudo yum install clickhouse-test-1.1.54383-1.el7.x86_64
sudo yum install clickhouse-client-1.1.54383-1.el7.x86_64
