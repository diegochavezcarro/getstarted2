# getstarted2
Modified example from https://docs.docker.com/get-started/

The idea is to test composer, swarm and docker-machine.

1. Create virtual machine

docker-machine create --driver virtualbox myvm1

2. Get IP:

docker-machine env myvm1

3. Should show created vms (there should be a default too)

docker-machine ls

4. Create a swarm manager. Use IP from step 2

docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.101"

5. Copy docker-compose.yml to master

docker-machine scp docker-compose.yml myvm1:~ 

6. Deploy app 

docker-machine ssh myvm1 "docker stack deploy -c docker-compose.yml getstartedlab"

7. Watch created containers in master:

docker-machine ssh myvm1 "docker ps"

8. Go to http://192.168.99.101 (or ip from step 2). Refresh several times to see different container id each time (load balancing). Go to http://192.168.99.101:8080 to see Visualizer.

9. Stop one of the containers from step 7.

docker-machine ssh myvm1 "docker stop 18a45d6311d8"

10. Repeat step 7. One of the containers is automatically restarted. Also see Visualizer and App (step 8).

11. Scale horizontally. Create other vm.

docker-machine create --driver virtualbox myvm2

12. Add myvm2 to swarm as a worker, using command from the step 4 response

docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-10v6ngr7n8a2oyu409q6nnb-34z391b63vyiz5rcltp0alpot 192.168.99.101:2377"

13. Repeat step 9. New virtual machine should start a new container. View apps from step 8.

14. Add other vm. Add this to swarm as a worker. Kill myvm2 (docker-machine rm myvm2) or just leave the swarm (docker-machine ssh myvm2 "docker swarm leave"). You should see containers automatically recreated in other vms. This command is stronger:

docker-machine ssh myvm1 "docker node rm myvm2 --force"


15. Any time you could see state of vms with these:

 docker-machine ssh myvm1 "docker node ls"
 
 docker-machine ls



