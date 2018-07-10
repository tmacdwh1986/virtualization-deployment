
yum -y update

yum install -y docker

service docker start

vi /etc/sysctl.conf
    net.ipv4.ip_forward=1

systemctl restart network

docker pull percona/percona-xtradb-cluster

docker tag percona/percona-xtradb-cluster pxc

docker rmi percona/percona-xtradb-cluster

docker network create --subnet=172.18.0.0/16 net1

docker volume create --name v1

docker volume create --name v2

docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -v v1:/var/lib/mysql -v backup:/data --privileged --name=node1 --net=net1 --ip 172.18.0.2 pxc

docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=abc123456 -e CLUSTER_NAME=PXC -e XTRABACKUP_PASSWORD=abc123456 -e CLUSTER_JOIN=node1 -v v2:/var/lib/mysql -v backup:/data --privileged --name=node2 --net=net1 --ip 172.18.0.3 pxc

docker pull haproxy

 
vi /home/soft/haproxy.cfg

docker run -it -d -p 4001:8888 -p 4002:3306 -v /home/soft/haproxy:/usr/local/etc/haproxy --name h1 --privileged --net=net1 --ip 172.18.0.7 haproxy

docker exec -it h1 bash

haproxy -f /usr/local/etc/haproxy/haproxy.cfg

docker run -it -d -p 4003:8888 -p 4004:3306 -v /home/soft/haproxy:/usr/local/etc/haproxy --name h2 --privileged --net=net1 --ip 172.18.0.8 haproxy

docker exec -it h2 bash

haproxy -f /usr/local/etc/haproxy/haproxy.cfg

# backend project
 
mvn clean install -Dmaven.test.skip=true

 
docker pull java

# for develop

docker volume create j1

docker run -it -d --name j1 -v j1:/home/soft --net=host java

docker exec -it j1 bash

nohup java -jar /home/soft/renren-fast.jar

docker volume create j2

docker run -it -d --name j2 -v j2:/home/soft --net=host java

docker exec -it j2 bash

nohup java -jar /home/soft/renren-fast.jar

docker pull nginx

vi /home/n1/nginx.conf

docker run -it -d --name n1 -v /home/n1/nginx.conf:/etc/nginx/nginx.conf --net=host --privileged nginx



