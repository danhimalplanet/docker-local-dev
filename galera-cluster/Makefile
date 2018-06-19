# Makefile
#
# all cribbed from https://hub.docker.com/r/panubo/mariadb-galera/

include .env

mysql_root_password = coffee

test-env:
	echo ${data_mount}

tail1:
	docker logs node1 -f

tail2:
	docker logs node2 -f

tail3:
	docker logs node3 -f

prompt1:
	docker exec -it node1 /usr/bin/mysql --user=root --password=$(mysql_root_password)

prompt2:
	docker exec -it node2 /usr/bin/mysql --user=root --password=$(mysql_root_password)

prompt3:
	docker exec -it node3 /usr/bin/mysql --user=root --password=$(mysql_root_password)

create-network:
	docker network create galera

nuke:
	sudo rm -rf $(data_mount)/galera.service/node1/*
	sudo rm -rf $(data_mount)/galera.service/node2/*
	sudo rm -rf $(data_mount)/galera.service/node3/*
	docker stop node1
	docker rm node1
	docker stop node2
	docker rm node2
	docker stop node3
	docker rm node3

node1:
	docker run -d --net host \
		--name node1 \
		--network galera \
  		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node1) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
  		-e MYSQL_ROOT_PASSWORD=$(mysql_root_password) \
  		-v $(data_mount)/galera.service/node1:/var/lib/mysql:Z \
  		panubo/mariadb-galera \
    	mysqld \
    	--wsrep-new-cluster

node2:
	mkdir -p $(data_mount)/galera.service/node2/mysql
	docker run -d --net host \
		--name node2 \
		--network galera \
		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node2) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
		-v $(data_mount)/galera.service/node2:/var/lib/mysql:Z \
		panubo/mariadb-galera \
		mysqld

node3:
	mkdir -p $(data_mount)/galera.service/node3/mysql
	docker run -d --net host \
		--name node3 \
        --network galera \
        -e WSREP_NODE_ADDRESS=$(wsrep_node_address_node3) \
        -e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
        -v $(data_mount)/galera.service/node3:/var/lib/mysql:Z \
        panubo/mariadb-galera \
        mysqld
