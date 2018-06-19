# Makefile
#
# all cribbed from https://hub.docker.com/r/panubo/mariadb-galera/

mysql_root_password = coffee
wsrep_node_address_node-1 = 172.19.0.2
wsrep_node_address_node-2 = 172.19.0.3
wsrep_node_address_node-3 = 172.19.0.4

wsrep_cluster_address = gcomm://172.19.0.2:4567,172.19.0.3:4567,172.19.0.4:4567

data_mount = /Users/user/data

shell-node-1:
	docker exec -it galera-node-1 /usr/bin/mysql --user=root --password=$(mysql_root_password)

create-network:
	docker network create galera

nuke:
	sudo rm -rf /Users/user/data/galera.service/

bootstrap-node-1:
	docker run -d --net host \
		--name galera-node-1 \
		--network galera \
  		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node-1) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
  		-e MYSQL_ROOT_PASSWORD=$(mysql_root_password) \
  		-p 3306:3306 \
  		-p 4567:4567/udp \
  		-p 4567-4568:4567-4568 \
  		-p 4444:4444 \
  		-v $(data_mount)/galera.service/mysql:/var/lib/mysql:Z \
  		panubo/mariadb-galera \
    	mysqld \
    	--wsrep-new-cluster

bootstrap-node-2:
	mkdir -p $(data_mount)/galera.service/mysql/mysql
	docker run -d --net host \
		--name galera-node-2 \
		--network galera \
		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node-2) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
		-p 3306:3306 \
		-p 4567:4567/udp \
		-p 4567-4568:4567-4568 \
		-p 4444:4444 \
		-v $(data_mount)/galera.service/mysql:/var/lib/mysql:Z \
		panubo/mariadb-galera \
		mysqld
