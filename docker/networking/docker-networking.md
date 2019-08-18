# Docker networks summary
Main issues I have found are in trying to address the host, 
it is often pretty easy to connect dockers, but because of how the Daemon is setup 
there are some interesting things to be weary of. 

---------------
### Network essentials recap: 
---
TCP vs UDP
---
- A **TCP (Transmission Control Protocol)** connection is uniquely identified by the 4-tuple (source address, source port, dest address, dest port).
- A **UDP (User Datagram Protocol)** UDP uses a simple connectionless communication model with a minimum of protocol mechanisms. It has 
no handshaking dialogues, and thus exposes the user's program to any unreliability of the underlying network; there is no guarantee of 
delivery, ordering, or duplicate protection. 

**Basically, TCP is the better one, UDP is less secure etc, 
should only be used in cases where it is specifically required, 
often only in legacy applications.**

Ports
---
A port is just a 16-bit integer, used to distinguish between multiple 
active sockets on the same host, but there are certain conventions 
governing their allocation: 

1. **"well-known" ports are < 1024.**
    - 80 is the "well-known" port for HTTP, which means it's the default
     unless your URL specifies otherwise. 
    - These ports are generally protected,
     so an unprivileged process cannot bind one.
3. "**registered" ports from 1024 and 49152**, useable by unprivileged 
processes to provide services 
    - 8080 is commonly used for an unprivileged HTTP server
4. **remaining values from 49152 to 65535** are used for ephemeral ports.  
    - When you create a socket and connect to a server, 
    without binding your socket to a particular local port, 
    the kernel assigns a free port from the ephemeral range. 
    - This is just to create a unique 4-tuple identifying your connection, and you'll normally never care what the value is.
    - The actual range used for ephemeral ports may vary by OS and even be configurable - it'll always start above 1024 though.


---------------
### host networking
- Probably the most simple method of being able to communicate with the host
- All it does is basically force the container to join the host's network, 
- By far and away the least secure method of creating containers, because 
the container can be addressed externally by port, and Docker Daemon's 
often have root access to the host if the application is unsecure you could 
have a very big security hole 

##### Implementation notes
###### Addressing
- After joining the network you address the host as below:
    -       127.0.0.1
- Because the docker is not hosted in the docker network it no longer has 
the use of the "links" section to address other containers/services, 
because of this:
    - To address other containers you can do whatever you would do on the host, 
    depending on the implementation you will need to go and get the IP or port 
    or the container and include  this. 
- Because it can be addressed externally you will also likely need to map the port
of the application and be weary and potential security issues the application 
hosted on the port, 

###### Docker compose    
- In docker compose add:
    -       network_mode: host

### bridge networking
- Default docker networking mode
- Creates a bridge between the docker network and the host's 
network
    
- built on eth0 adapter by default. 
- Can also be implemented as custom network if more control needed.   
##### Implementation notes
###### Addressing
- Makes it slightly more complicated to work out the hosts's IP
- Can be done through using ip addr show or env variables
- Python snippet for grabbing the host IP : 
    -       def determine_docker_host_ip_address():
            cmd = "ip route show"
            import subprocess
            process = subprocess.Popen(cmd.split(), stdout=subprocess.PIPE)
            output, error = process.communicate()
            return str(output).split(' ')[2]
- Makes it much easier to address other docker containers uses links
    in docker compose  
###### Docker compose   
- Don't really need to make any changes here. 
- Good positive that you can address containers through link: 


### mac-vlan networking
- Most complicated but full-featured type of networking to setup
- Requires changes in just about all config 
- Allows for assigning virtual mac addresses, meaning your docker 
container can get a full fledged IP address and be accessed 
like a host
##### Implementation notes
###### Addressing
###### Docker compose   