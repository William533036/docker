4-1 本章概述和实验环境介绍
	单机
		bridge Network
		Host Network
		None Network
	多机
		Overlay Network
	
	4-3 Linux网络命名空间
	ip netns list
	ip netns add test1
	ip netns delete test1
	ip netns exec test1 ip a
	ip netns exec test1 ip link
	ip netns exec test1 ip link set  dev lo up
	ip link add veth-test1 type veth peer name veth-test2
	ip link set veth-test1 nets test1
	ip link set veth-test2 nets test2
	ip netns exec test1 ip link set  veth-test1 up
	ip netns exec test2 ip link set  veth-test2 up



	4-4 Docker bridge0详解
	docker ps
	docker network ls
	docker network inspect
	yum binstall brideg-utils
	brctl show
