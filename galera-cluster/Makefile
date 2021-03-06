# Makefile
#
# all cribbed from https://hub.docker.com/r/panubo/mariadb-galera/

include .env

# use proper subnet and ip addrs for your nodes

wsrep_subnet = 172.40.0.0/16

wsrep_node_address_node1 = 172.40.0.2
wsrep_node_address_node2 = 172.40.0.3
wsrep_node_address_node3 = 172.40.0.4

wsrep_cluster_address = gcomm://172.40.0.2:4567,172.40.0.3:4567,172.40.0.4:4567

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
	docker network create galera --subnet=$(wsrep_subnet)

nuke:
	sudo rm -rf $(data_mount)/galera.service/node1/*
	sudo rm -rf $(data_mount)/galera.service/node2/*
	sudo rm -rf $(data_mount)/galera.service/node3/*
	docker stop node1 || true
	docker rm node1 || true
	docker stop node2 || true
	docker rm node2 || true
	docker stop node3 || true
	docker rm node3 || true

node1:
	docker run -d --net=galera \
		--name node1 \
		--ip=$(wsrep_node_address_node1) \
  		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node1) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
  		-e MYSQL_ROOT_PASSWORD=$(mysql_root_password) \
  		-v $(data_mount)/galera.service/node1:/var/lib/mysql:Z \
  		panubo/mariadb-galera \
    	mysqld \
    	--wsrep-new-cluster

node2:
	mkdir -p $(data_mount)/galera.service/node2/mysql
	docker run -d --net=galera \
		--name node2 \
		--ip=$(wsrep_node_address_node2) \
		--network galera \
		-e WSREP_NODE_ADDRESS=$(wsrep_node_address_node2) \
		-e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
		-v $(data_mount)/galera.service/node2:/var/lib/mysql:Z \
		panubo/mariadb-galera \
		mysqld

node3:
	mkdir -p $(data_mount)/galera.service/node3/mysql
	docker run -d --net=galera \
		--name node3 \
		--ip=$(wsrep_node_address_node3) \
        --network galera \
        -e WSREP_NODE_ADDRESS=$(wsrep_node_address_node3) \
        -e WSREP_CLUSTER_ADDRESS=$(wsrep_cluster_address) \
        -v $(data_mount)/galera.service/node3:/var/lib/mysql:Z \
        panubo/mariadb-galera \
        mysqld

shutdown-node1:
	docker exec -it node1 /usr/bin/mysqladmin --user=root --password=$(mysql_root_password) shutdown || true

shutdown-node2:
	docker exec -it node2 /usr/bin/mysqladmin --user=root --password=$(mysql_root_password) shutdown || true

shutdown-node3:
	docker exec -it node3 /usr/bin/mysqladmin --user=root --password=$(mysql_root_password) shutdown || true

shutdown: shutdown-node3 shutdown-node2 shutdown-node1

status:
	docker exec -it node1 /usr/bin/mysql --user=root --password=$(mysql_root_password) -e "show status like 'wsrep_cluster_size'"
