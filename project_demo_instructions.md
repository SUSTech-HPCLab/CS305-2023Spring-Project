# Project Demo Instructions

You are required to demonstrate your project to the graders in Lab 16. We will check the following functions of your code. Please READ CAREFULLY and prepare test cases for the demo.

Please remember to submit your code to Sakai after the demonstration.

## DHCP

1. Use the default config settings (i.e., start_ip, end_ip, subnet mask) and create a topo with a switch and one host (using Python script). Assign a valid IP address correctly to the host.

2. Use the default config settings (i.e., start_ip, end_ip, subnet mask) and create a topo with a switch and multiple hosts (using Python script). Assign valid IP addresses correctly to all the hosts.

3. Change the `start_ip`, `end_ip`, and subnet mask, and create a topo with a switch and multiple hosts (using Python script). Assign new valid IP addresses correctly to all the hosts.

4. Assume that the number of IP addresses between `start_ip` and `end_ip` is n. Create a topo with a switch and m hosts (using Python script), where m < n. In this case, the first m hosts are assigned valid IP addresses, and the remaining n-m hosts do not receive an IP address.

## Shortest path switching

1. Pass the basic test case (available in the GitHub repository) to ensure all hosts are reachable. Use `pingall` to check connectivity.
   - Upon each change in the network topology (e.g., add a switch, add a link, etc.), print the current topology structure and the shortest path (and length) between any two switches in the controller console (e.g., s1 to s2: s1 -> s3 -> s4 -> s2, 3 edges).
   - After the topology is established, use `pingall` to verify connectivity between all hosts.



2. Provide a complex test case
   - The test case should have more than 6 hosts, more than 6 switches, and more than 10 edges.
   - The test case should support initializing the topology (from Python script, similar to the basic test case) and dynamically changing the topology using the Mininet CLI.
   - The changes should cover the following topology modification operations: `handle_switch_add`, `handle_switch_delete`, `handle_host_add`, `handle_link_add`, `handle_link_delete`, `handle_port_modify`.
   - After each modification, print the current topology structure with a third-party library (e.g., networkx) and the shortest path (and length) between any two switches in the controller console (e.g., s1 to s2: s1 -> s3 -> s4 -> s2, 3 edges).

## Hints

1. Mininet commands that you may find useful:
   - `MN > switch s1 stop/start` (Stop/Start switch s1)
   - `MN > link h1 s1 down/up` (Bring down/Bring up the link between host h1 and switch s1)
   - `MN > sh ovs-ofctl mod-port s1 1 down` (Disable port 1 on switch s1 using OpenFlow)
2. When creating hosts using Python script, please follow the basic test case in Github to create hosts without IPs. like  `h1 = self.addHost('h1', ip='no ip defined/8')`

