# Makefile
#
# all cribbed from https://hub.docker.com/r/panubo/mariadb-galera/

include .env

mysql_root_password = coffee
wsrep_node_address_node-1 = 172.19.0.2
wsrep_node_address_node-2 = 172.19.0.3
wsrep_node_address_node-3 = 172.19.0.4

wsrep_cluster_address = gcomm://172.19.0.2:4567,172.19.0.3:4567,172.19.0.4:4567

#data_mount = /Users/user/data

test-env:
	echo ${var1}
	echo ${data_mount}

prompt:
	docker exec -it node-1 /usr/bin/mysql --user=root --password=$(mysql_root_password)

create-network:
	docker network create galera

nuke:
	sudo rm -rf /Users/user/data/galera.service/*
	docker stop node-1
	docker rm node-1
	docker stop node-2
	docker rm node-2
	docker stop node-3
	docker rm node-3

node-1:
	docker run -d --net host \
		--name node-1 \
		--network galera \
  		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node-1) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
  		-e MYSQL_ROOT_PASSWORD=$(mysql_root_password) \
  		-v $(data_mount)/galera.service/node1:/var/lib/mysql:Z \
  		panubo/mariadb-galera \
    	mysqld \
    	--wsrep-new-cluster

node-2:
	mkdir -p $(data_mount)/galera.service/node2/mysql
	docker run -d --net host \
		--name node-2 \
		--network galera \
		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node-2) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
		-v $(data_mount)/galera.service/node2:/var/lib/mysql:Z \
		panubo/mariadb-galera \
		mysqld

node-3:
	mkdir -p $(data_mount)/galera.service/node3/mysql
	docker run -d --net host \
		--name node-3 \
        --network galera \
        -e WSREP_NODE_ADDRESS=$(wsrep_node_address_node-3) \
        -e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
        -v $(data_mount)/galera.service/node3:/var/lib/mysql:Z \
        panubo/mariadb-galera \
        mysqld

