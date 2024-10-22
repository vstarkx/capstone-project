# Capstone Project Description
This Flask application serves as a simple web interface for adding users and viewing user details. Below is a brief description of the files and functionalities:

- **app.py**: 
  - The main Flask application file.
  - It integrates with **Redis** for caching and **MySQL** for persistent data storage.
  - Provides routes for:
    - The homepage (`/`) that renders a form to add new users.
    - A `/add_user` route for submitting new users, which stores the data in both MySQL and Redis.
    - A `/user/<user_id>` route for fetching user information, first checking Redis (cache) and then falling back to MySQL if the data isn't found in the cache.
  - It also contains a function to initialize the MySQL database table for users.

- **test_app.py**: 
  - Contains unit test to verify that the Flask application is working correctly.
  - Tests the home page route (`/`) to ensure it returns a successful response.
  - The test can be run using command: `python test_app.py`

- **templates/index.html**: 
  - The homepage HTML template.
  - Displays a form that allows users to input and submit a username.

- **templates/user.html**: 
  - The user information HTML template.
  - Displays the user’s ID, name, and the source (either Redis or MySQL) where the information was retrieved from.

This application demonstrates basic operations with integration between a relational database (MySQL) and an in-memory store (Redis) for faster data retrieval. Bellow is a screenshoot of the Homepage, I'll add new user:


<img width="659" alt="Screen Shot 1446-04-19 at 1 35 19 PM" src="https://github.com/user-attachments/assets/2eb60bbb-0b2a-4d43-aa07-8a2b648032db">


Notice that after visiting `/user/<user_id>` the Source shows that data came from Redis:

<img width="472" alt="Screen Shot 1446-04-19 at 1 42 34 PM" src="https://github.com/user-attachments/assets/8a99c296-fd68-4854-aa04-491f0a7ebc8d">


### Now if I kill and re-run Redis Container the Source will show that data are fetched from MySQL:

- Get Redis container id:
```
➜  capstone git:(main) ✗ docker ps
CONTAINER ID   IMAGE                COMMAND                  CREATED         STATUS              PORTS                               NAMES
f2d98ffdfc18   redis:latest         "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes        0.0.0.0:6379->6379/tcp              capstone-redis-1
c3511a9f6c4f   mysql:latest         "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   capstone-db-1
2fee8a71e0fc   capstone-flask-app   "python app.py"          9 minutes ago   Up About a minute   0.0.0.0:80->5000/tcp                capstone-flask-app-1
```


- Kill Redis container:
  
```
➜  capstone git:(main) ✗ docker kill f2d98ffdfc18
f2d98ffdfc18
```

- Re-run Redis container:

```
➜  capstone git:(main) ✗ docker compose up redis
[+] Running 1/0
 ⠿ Container capstone-redis-1  Created                                                                                                                            0.0s
Attaching to capstone-redis-1
capstone-redis-1  | 1:C 22 Oct 2024 10:44:06.942 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
capstone-redis-1  | 1:C 22 Oct 2024 10:44:06.942 * Redis version=7.2.5, bits=64, commit=00000000, modified=0, pid=1, just started
capstone-redis-1  | 1:C 22 Oct 2024 10:44:06.942 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
capstone-redis-1  | 1:M 22 Oct 2024 10:44:06.943 * monotonic clock: POSIX clock_gettime
capstone-redis-1  | 1:M 22 Oct 2024 10:44:06.944 * Running mode=standalone, port=6379.
capstone-redis-1  | 1:M 22 Oct 2024 10:44:06.944 * Server initialized
capstone-redis-1  | 1:M 22 Oct 2024 10:44:06.944 * Ready to accept connections tcp
```
- Notince that Source changed to MySQL:


<img width="498" alt="Screen Shot 1446-04-19 at 1 44 24 PM" src="https://github.com/user-attachments/assets/84d9b3c9-41a4-4e74-8441-0be7e71f866b">


# Project Requirements:

### Project Requirements

#### 1. **Dockerfile for Flask Application**:
   - A `Dockerfile` is required to containerize the Flask application.

#### 2. **Docker Compose Configuration**:
   - A `compose.yml` file should be created to handle the multi-service setup, which includes:
     - **Flask Application**:
       - The service should build the Flask application image using the `Dockerfile`.
     - **Redis Service**:
       - Use the official `redis` image.
     - **MySQL Service**:
       - Use the official `mysql` image.
   - Environment Variables:
       - Ensure that the Flask application can access Redis and MySQL using environment variables.
    
#### 3. **Infrastructure as Code Using Terraform**:
   - Write a Terraform file to create an infrastructure on Alibaba Cloud that meets the following requirements:
     - **Redis Server**: Redis server in the private vSwitch which accepts connections from Http Servers.
     - **MySQL Server**: MySQL server in the private vSwitch which accepts connections from Http Servers.
     - **Http Servers**: a total of 2 Http servers in the private vSwitch.
     - **Load Balancer**: a Network Load Balancer which distribute traffic to the http server group.
     - **Runner Server**: a bastion/runner server which can be used to ssh to the servers in the private vSwitches. and can be used as a self-hosted runner later in the GitHub Actions Workflows.
     - **Nat Gateway**: NatGatway server that allow servers in the private vSwitch to access the internet.
       
The following sketch demonstrates the infrastructure design:


![capstone](https://github.com/user-attachments/assets/0bbcee9b-ec80-4c9b-a938-e978d0d467bf)

