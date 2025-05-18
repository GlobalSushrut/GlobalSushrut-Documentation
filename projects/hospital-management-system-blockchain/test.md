# hospital-management-system-blockchain - test

*This documentation is from the private repository hospital-management-system-blockchain.*

---

  |
4 | use uuid::Uuid;
  |     ^^^^^^^^^^

warning: unused import: `Algorithm`
 --> src/security.rs:5:82
  |
5 | ...lidation, Algorithm};
  |              ^^^^^^^^^

warning: unused variable: `encryption_key`
  --> src/security.rs:26:9
   |
26 |         encryption_key: &str
   |         ^^^^^^^^^^^^^^ help: if this is intentional, prefix it with an underscore: `_encryption_key`
   |
   = note: `#[warn(unused_variables)]` on by default

warning: unused variable: `encryption_key`
  --> src/security.rs:46:9
   |
46 |         encryption_key: &str
   |         ^^^^^^^^^^^^^^ help: if this is intentional, prefix it with an underscore: `_encryption_key`

warning: fields `environment` and `jwt_secret` are never read
  --> src/config.rs:9:9
   |
6  | pub struct Config {
   |            ------ fields in this struct
...
9  |     pub environment: Environment,
   |         ^^^^^^^^^^^
10 |     pub allowed_origins: Vec<H...
11 |     pub jwt_secret: String,
   |         ^^^^^^^^^^
   |
   = note: `Config` has derived impls for the traits `Debug` and `Clone`, but these are intentionally ignored during dead code analysis
   = note: `#[warn(dead_code)]` on by default

warning: variants `Authentication`, `Authorization`, `InternalServer`, and `Encryption` are never constructed
  --> src/error.rs:12:5
   |
10 | pub enum AppError {
   |          -------- variants in this enum
11 |     #[error("Authenticat...
12 |     Authentication(String),
   |     ^^^^^^^^^^^^^^
...
15 |     Authorization(String),
   |     ^^^^^^^^^^^^^
...
27 |     InternalServer(String),
   |     ^^^^^^^^^^^^^^
...
30 |     Encryption(String),
   |     ^^^^^^^^^^
   |
   = note: `AppError` has a derived impl for the trait `Debug`, but this is intentionally ignored during dead code analysis

warning: field `decrypt_fields` is never read
  --> src/models.rs:37:9
   |
34 | ...truct QueryDocumentsRequest {
   |          --------------------- field in this struct
...
37 | ...ub decrypt_fields: Vec<String>,
   |       ^^^^^^^^^^^^^^
   |
   = note: `QueryDocumentsRequest` has a derived impl for the trait `Debug`, but this is intentionally ignored during dead code analysis

warning: function `decrypt_fields` is never used
  --> src/security.rs:43:12
   |
43 |     pub fn decrypt_fields(
   |            ^^^^^^^^^^^^^^

warning: function `generate_token` is never used
  --> src/security.rs:72:12
   |
72 |     pub fn generate_token(user_...
   |            ^^^^^^^^^^^^^^

warning: function `validate_token` is never used
  --> src/security.rs:94:12
   |
94 |     pub fn validate_token(token...
   |            ^^^^^^^^^^^^^^

warning: function `validate_api_key` is never used
   --> src/security.rs:111:12
    |
111 |     pub fn validate_api_key(ap...
    |            ^^^^^^^^^^^^^^^^

warning: `rust-secure-db` (bin "rust-secure-db") generated 15 warnings (run `cargo fix --bin "rust-secure-db"` to apply 6 suggestions)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 14.67s
     Running `target/debug/rust-secure-db`
2025-03-09T22:43:32.999988Z  WARN rust_secure_db: Database connection failed, but SQLX_OFFLINE=true: error returned from database: password authentication failed for user "postgres"
2025-03-09T22:43:33.000531Z  INFO rust_secure_db: Running in offline development mode with mock database
2025-03-09T22:43:33.001530Z  INFO rust_secure_db: Listening on 0.0.0.0:8000
for python 
2025-03-09 18:45:24,239 - werkzeug - INFO -  * Restarting with stat
2025-03-09 18:45:25,094 - app.middleware - INFO - Middlewares registered successfully
2025-03-09 18:45:25,095 - app - INFO - Secure Rust database is ENABLED
2025-03-09 18:45:25,095 - app - INFO - Secure entities: ['medical_records', 'patient_data', 'prescriptions', 'payment_info', 'user_credentials', 'patient', 'doctor', 'appointment', 'medical_history', 'lab_results', 'diagnosis', 'insurance', 'billing', 'medication', 'staff', 'admin', 'personal_data']
2025-03-09 18:45:25,096 - app.auth - WARNING - ================================================================================
2025-03-09 18:45:25,096 - app.auth - WARNING - DEVELOPER MODE ACTIVE: Authentication and authorization bypassed
2025-03-09 18:45:25,096 - app.auth - WARNING - DO NOT USE IN PRODUCTION ENVIRONMENT
2025-03-09 18:45:25,096 - app.auth - WARNING - ================================================================================
2025-03-09 18:45:26,566 - app.services.blockchain_service - INFO - Running in development mode - using mock Web3
2025-03-09 18:45:26,566 - app.services.blockchain_service - INFO - Running in development mode - using mock contract
2025-03-09 18:45:26,567 - app.services.blockchain_service - WARNING - No private key provided. Read-only mode enabled.
2025-03-09 18:45:26,567 - app.services.blockchain_service - INFO - BlockchainService initialized. Connected to network: localhost
2025-03-09 18:45:26,567 - app.services.blockchain_service - INFO - Running in development mode - using mock Web3
2025-03-09 18:45:26,568 - app.services.blockchain_service - INFO - Running in development mode - using mock contract
2025-03-09 18:45:26,568 - app.services.blockchain_service - WARNING - No private key provided. Read-only mode enabled.
2025-03-09 18:45:26,568 - app.services.blockchain_service - INFO - BlockchainService initialized. Connected to network: localhost
Blockchain routes registered successfully
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
2025-03-09 18:45:26,603 - werkzeug - WARNING -  * Debugger is active!
2025-03-09 18:45:26,704 - werkzeug - INFO -  * Debugger PIN: 445-142-901

Not auto-run
Thought Process
I need to check on the status of the Python backend that's running. Let me check the command status.

Let me check the status of the Python backend that's running:

Checked command status
Thought Process
I need to let the user know that the Python backend for the Hospital Management System is now running successfully. I'll provide details about what's happening:

The Python backend for the Hospital Management System is now running successfully!

Here's what's happening:

Server Status: The Flask application is running in debug mode on:
http://127.0.0.1:5000
http://192.168.100.134:5000 (on your local network)
Development Mode Notifications:
Authentication and authorization are bypassed (for development only)
Debug mode is active with Debugger PIN: 445-142-901
Service Initializations:
Blockchain Service: Running in mock mode (development)
Secure Rust Database: ENABLED
Secure entities are properly configured
Important Notes:
The app shows warnings that the authentication is in developer mode - this is expected in a development environment
The blockchain service is using mock contracts since we're in a development environment
The backend is now ready to handle requests. You can:

Access the API endpoints through the URLs mentioned above
Make requests to the backend from the frontend application
Test the integration with the Rust secure database (which we set up to run in offline mode earlier)
The system is running in development mode with appropriate security warnings. This setup is perfect for local development and testing.

blockchain  

 > [8/8] RUN geth --datadir=/root/data init /root/genesis.json:
0.443 Fatal: invalid genesis file: json: cannot unmarshal hex string of odd length into Go struct field Genesis.extraData of type hexutil.Bytes
0.444 Fatal: invalid genesis file: json: cannot unmarshal hex string of odd length into Go struct field Genesis.extraData of type hexutil.Bytes
------
Dockerfile.geth:18
--------------------
  16 |     
  17 |     # Initialize the genesis block
  18 | >>> RUN geth --datadir=/root/data init /root/genesis.json
  19 |     
  20 |     # Expose default Ethereum port
--------------------
ERROR: failed to solve: process "/bin/sh -c geth --datadir=/root/data init /root/genesis.json" did not complete successfully: exit code: 1
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/medical-chain'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/medical-chain$ docker-compose up -d
[+] Running 3/3
 ✔ Container validator3  Started  1.0s 
 ✔ Container validator1  Started  1.2s 
 ✔ Container validator2  Started  1.2s 
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/medical-chain$ 
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ cd docker/blockchain && docker build -t hospital-blockchain -f Dockerfile.geth .
[+] Building 0.6s (7/12)  docker:defaul[+] Building 0.7s (8/12)  docker:defaul[+] Building 0.8s (9/12)  docker:defaul[+] Building 1.0s (9/12)  docker:defaul[+] Building 1.1s (9/12)  docker:defaul[+] Building 1.3s (9/12)  docker:defaul[+] Building 1.3s (10/12)  docker:defau[+] Building 1.4s (10/12)  docker:defau[+] Building 1.6s (10/12)  docker:defau[+] Building 1.7s (10/12)  docker:defau[+] Building 1.8s (11/12)  docker:defau[+] Building 2.0s (11/12)  docker:defau[+] Building 2.1s (11/12)  docker:defau[+] Building 2.2s (11/12)  docker:defau[+] Building 2.4s (12/12) FINISHED docker:defaultFROM docker.io/ethereu  0.0s
 => [internal] load build defini  0.0s
 => => transferring dockerf 605B  0.0s
 => [internal] load metadata for  0.3s
 => [internal] load .dockerignor  0.0s
 => => transferring context: 2B   0.0s
 => [1/8] FROM docker.io/ethereu  0.0s
 => [internal] load build contex  0.0s
 => => transferring conte 1.28kB  0.0s
 => CACHED [2/8] WORKDIR /root    0.0s
 => [3/8] COPY ./genesis.json /r  0.1s
 => [4/8] COPY ./password.txt /r  0.1s
 => [5/8] COPY ./start-geth.sh /  0.1s
 => [6/8] RUN chmod +x /root/sta  0.5s
 => [7/8] RUN mkdir -p /root/dat  0.5s
 => ERROR [8/8] RUN geth --datad  0.5s
------
 > [8/8] RUN geth --datadir=/root/data init /root/genesis.json:
0.443 Fatal: invalid genesis file: json: cannot unmarshal hex string of odd length into Go struct field Genesis.extraData of type hexutil.Bytes
0.444 Fatal: invalid genesis file: json: cannot unmarshal hex string of odd length into Go struct field Genesis.extraData of type hexutil.Bytes
------
Dockerfile.geth:18
--------------------
  16 |     
  17 |     # Initialize the genesis block
  18 | >>> RUN geth --datadir=/root/data init /root/genesis.json
  19 |     
  20 |     # Expose default Ethereum port
--------------------
ERROR: failed to solve: process "/bin/sh -c geth --datadir=/root/data init /root/genesis.json" did not complete successfully: exit code: 1
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/medical-chain'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/medical-chain$ docker-compose up -d
[+] Running 3/3
 ✔ Container validator3  Started  1.0s 
 ✔ Container validator1  Started  1.2s 
 ✔ Container validator2  Started  1.2s 
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/medical-chain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ cd docker/blockchain && docker build -t hospital-blockchain -f Dockerfile.geth .
[+] Building 0.4s (7/12)  docker:defaul[+] Building 0.5s (8/12)  docker:defaul[+] Building 0.6s (9/12)  docker:defaul[+] Building 0.8s (9/12)  docker:defaul[+] Building 1.0s (9/12)  docker:defaul[+] Building 1.1s (9/12)  docker:defaul[+] Building 1.2s (10/12)  docker:defau[+] Building 1.4s (10/12)  docker:defau[+] Building 1.5s (10/12)  docker:defau[+] Building 1.7s (10/12)  docker:defau[+] Building 1.7s (11/12)  docker:defau[+] Building 1.9s (11/12)  docker:defau[+] Building 2.0s (11/12)  docker:defau[+] Building 2.2s (11/12)  docker:defau[+] Building 2.2s (11/12)  docker:defau[+] Building 2.4s (12/12) FINISHED docker:defaultFROM docker.io/ethereu  0.0s
 => [internal] load build defini  0.0s
 => => transferring dockerf 605B  0.0s
 => [internal] load metadata for  0.2s
 => [internal] load .dockerignor  0.0s
 => => transferring context: 2B   0.0s
 => [1/8] FROM docker.io/ethereu  0.0s
 => [internal] load build contex  0.0s
 => => transferring conte 1.11kB  0.0s
 => CACHED [2/8] WORKDIR /root    0.0s
 => [3/8] COPY ./genesis.json /r  0.1s
 => [4/8] COPY ./password.txt /r  0.1s
 => [5/8] COPY ./start-geth.sh /  0.1s
 => [6/8] RUN chmod +x /root/sta  0.6s
 => [7/8] RUN mkdir -p /root/dat  0.5s
 => ERROR [8/8] RUN geth --datad  0.6s
------
 > [8/8] RUN geth --datadir=/root/data init /root/genesis.json:
0.513 Fatal: invalid genesis file: hex string of odd length
0.513 Fatal: invalid genesis file: hex string of odd length
------
Dockerfile.geth:18
--------------------
  16 |     
  17 |     # Initialize the genesis block
  18 | >>> RUN geth --datadir=/root/data init /root/genesis.json
  19 |     
  20 |     # Expose default Ethereum port
--------------------
ERROR: failed to solve: process "/bin/sh -c geth --datadir=/root/data init /root/genesis.json" did not complete successfully: exit code: 1
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ cd docker/blockchain && docker build -t hospital-blockchain -f Dockerfile.geth .
[+] Building 0.6s (7/12)  docker:defaul[+] Building 0.7s (8/12)  docker:defaul[+] Building 0.9s (9/12)  docker:defaul[+] Building 1.0s (9/12)  docker:defaul[+] Building 1.2s (9/12)  docker:defaul[+] Building 1.3s (9/12)  docker:defaul[+] Building 1.4s (10/12)  docker:defau[+] Building 1.6s (10/12)  docker:defau[+] Building 1.8s (10/12)  docker:defau[+] Building 1.9s (10/12)  docker:defau[+] Building 2.0s (11/12)  docker:defau[+] Building 2.2s (11/12)  docker:defau[+] Building 2.3s (11/12)  docker:defau[+] Building 2.5s (11/12)  docker:defau[+] Building 2.6s (11/12)  docker:defau[+] Building 2.7s (11/12)  docker:defau[+] Building 2.8s (12/12)  docker:defau[+] Building 3.0s (12/12)  docker:defau[+] Building 3.1s (12/12)  docker:defau[+] Building 3.3s (12/12)  docker:defau[+] Building 3.4s (12/12)  docker:defau[+] Building 3.5s (12/12) FINISHED docker:defaultFROM docker.io/ethereu  0.0s
 => [internal] load build defini  0.0s
 => => transferring dockerf 605B  0.0s
 => [internal] load metadata for  0.4s
 => [internal] load .dockerignor  0.0s
 => => transferring context: 2B   0.0s
 => [1/8] FROM docker.io/ethereu  0.0s
 => [internal] load build contex  0.0s
 => => transferring context 923B  0.0s
 => CACHED [2/8] WORKDIR /root    0.0s
 => [3/8] COPY ./genesis.json /r  0.1s
 => [4/8] COPY ./password.txt /r  0.1s
 => [5/8] COPY ./start-geth.sh /  0.1s
 => [6/8] RUN chmod +x /root/sta  0.6s
 => [7/8] RUN mkdir -p /root/dat  0.6s
 => ERROR [8/8] RUN geth --datad  0.8s
------
 > [8/8] RUN geth --datadir=/root/data init /root/genesis.json:
0.657 Fatal: invalid genesis file: hex string of odd length
0.657 Fatal: invalid genesis file: hex string of odd length
------
Dockerfile.geth:18
--------------------
  16 |     
  17 |     # Initialize the genesis block
  18 | >>> RUN geth --datadir=/root/data init /root/genesis.json
  19 |     
  20 |     # Expose default Ethereum port
--------------------
ERROR: failed to solve: process "/bin/sh -c geth --datadir=/root/data init /root/genesis.json" did not complete successfully: exit code: 1
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ cd docker/blockchain && docker build -t hospital-blockchain -f Dockerfile.geth .
[+] Building 0.5s (7/12)  docker:defaul[+] Building 0.6s (8/12)  docker:defaul[+] Building 0.8s (9/12)  docker:defaul[+] Building 0.9s (9/12)  docker:defaul[+] Building 1.1s (9/12)  docker:defaul[+] Building 1.2s (9/12)  docker:defaul[+] Building 1.2s (10/12)  docker:defau[+] Building 1.4s (10/12)  docker:defau[+] Building 1.5s (10/12)  docker:defau[+] Building 1.7s (10/12)  docker:defau[+] Building 1.8s (11/12)  docker:defau[+] Building 1.9s (11/12)  docker:defau[+] Building 2.1s (11/12)  docker:defau[+] Building 2.2s (11/12)  docker:defau[+] Building 2.4s (11/12)  docker:defau[+] Building 2.5s (12/12) FINISHED docker:defaultFROM docker.io/ethereu  0.0s
 => [internal] load build defini  0.0s
 => => transferring dockerf 605B  0.0s
 => [internal] load metadata for  0.2s
 => [internal] load .dockerignor  0.0s
 => => transferring context: 2B   0.0s
 => [1/8] FROM docker.io/ethereu  0.0s
 => [internal] load build contex  0.0s
 => => transferring context 985B  0.0s
 => CACHED [2/8] WORKDIR /root    0.0s
 => [3/8] COPY ./genesis.json /r  0.1s
 => [4/8] COPY ./password.txt /r  0.2s
 => [5/8] COPY ./start-geth.sh /  0.1s
 => [6/8] RUN chmod +x /root/sta  0.5s
 => [7/8] RUN mkdir -p /root/dat  0.5s
 => ERROR [8/8] RUN geth --datad  0.6s
------
 > [8/8] RUN geth --datadir=/root/data init /root/genesis.json:
0.584 Fatal: invalid genesis file: hex string of odd length
0.584 Fatal: invalid genesis file: hex string of odd length
------
Dockerfile.geth:18
--------------------
  16 |     
  17 |     # Initialize the genesis block
  18 | >>> RUN geth --datadir=/root/data init /root/genesis.json
  19 |     
  20 |     # Expose default Ethereum port
--------------------
ERROR: failed to solve: process "/bin/sh -c geth --datadir=/root/data init /root/genesis.json" did not complete successfully: exit code: 1
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ cd docker/blockchain && docker build -t hospital-blockchain -f Dockerfile.geth .
[+] Building 0.4s (7/12)  docker:defaul[+] Building 0.6s (8/12)  docker:defaul[+] Building 0.7s (8/12)  docker:defaul[+] Building 0.7s (9/12)  docker:defaul[+] Building 0.9s (9/12)  docker:defaul[+] Building 1.0s (9/12)  docker:defaul[+] Building 1.2s (9/12)  docker:defaul[+] Building 1.2s (10/12)  docker:defau[+] Building 1.4s (10/12)  docker:defau[+] Building 1.5s (10/12)  docker:defau[+] Building 1.7s (10/12)  docker:defau[+] Building 1.8s (11/12)  docker:defau[+] Building 1.9s (11/12)  docker:defau[+] Building 2.1s (11/12)  docker:defau[+] Building 2.2s (11/12)  docker:defau[+] Building 2.3s (11/12)  docker:defau[+] Building 2.4s (11/12)  docker:defau[+] Building 2.6s (12/12)  docker:defau[+] Building 2.8s (12/13)  docker:defau[+] Building 2.9s (12/13)  docker:defau[+] Building 3.1s (13/13) FINISHED docker:defaultnsferring context 862B  0.0s
 => [internal] load build defini  0.0s
 => => transferring dockerf 605B  0.0s
 => [internal] load metadata for  0.3s
 => [internal] load .dockerignor  0.0s
 => => transferring context: 2B   0.0s
 => [1/8] FROM docker.io/ethereu  0.0s
 => [internal] load build contex  0.0s
 => => transferring context 862B  0.0s
 => CACHED [2/8] WORKDIR /root    0.0s
 => [3/8] COPY ./genesis.json /r  0.1s
 => [4/8] COPY ./password.txt /r  0.1s
 => [5/8] COPY ./start-geth.sh /  0.2s
 => [6/8] RUN chmod +x /root/sta  0.5s
 => [7/8] RUN mkdir -p /root/dat  0.5s
 => [8/8] RUN geth --datadir=/ro  0.8s
 => exporting to image            0.4s
 => => exporting layers           0.3s
 => => writing image sha256:ecde  0.0s
 => => naming to docker.io/libra  0.0s
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ docker run -d -p 8545:8545 -p 8546:8546 -p 30303:30303 -p 30303:30303/udp --name hospital-blockchain hospital-blockchain
f341058fbeb861184bd43b2ec6142628fc8c61849628ddf93eb47c3f8f7cbb76
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ docker logs hospital-blockchain
INFO [03-09|22:54:24.970] Maximum peer count                       ETH=50 LES=0 total=50
INFO [03-09|22:54:24.970] Smartcard socket not found, disabling    err="stat /run/pcscd/pcscd.comm: no such file or directory"
INFO [03-09|22:54:24.976] Set global gas cap                       cap=50,000,000
Using account: 
INFO [03-09|22:54:25.104] Maximum peer count                       ETH=50 LES=0 total=50
INFO [03-09|22:54:25.107] Smartcard socket not found, disabling    err="stat /run/pcscd/pcscd.comm: no such file or directory"
WARN [03-09|22:54:25.108] Option nousb is deprecated and USB is deactivated by default. Use --usb to enable 
Fatal: -miner.etherbase: invalid etherbase address ""
Fatal: -miner.etherbase: invalid etherbase address ""
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ docker stop hospital-blockchain && docker rm hospital-blockchain
hospital-blockchain
hospital-blockchain
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ cd docker/blockchain && docker build -t hospital-blockchain -f Dockerfile.geth .
[+] Building 1.0s (9/12)  docker:defaul[+] Building 1.1s (9/12)  docker:defaul[+] Building 1.3s (9/12)  docker:defaul[+] Building 1.4s (9/12)  docker:defaul[+] Building 1.4s (10/12)  docker:defau[+] Building 1.6s (10/12)  docker:defau[+] Building 1.7s (10/12)  docker:defau[+] Building 1.9s (10/12)  docker:defau[+] Building 1.9s (11/12)  docker:defau[+] Building 2.1s (11/12)  docker:defau[+] Building 2.3s (11/12)  docker:defau[+] Building 2.4s (11/12)  docker:defau[+] Building 2.5s (11/12)  docker:defau[+] Building 2.6s (11/12)  docker:defau[+] Building 2.8s (12/12)  docker:defau[+] Building 2.9s (12/13)  docker:defau[+] Building 3.0s (12/13)  docker:defau[+] Building 3.2s (13/13) FINISHED docker:defaultnsferring conte 1.20kB  0.0s
 => [internal] load build defini  0.1s
 => => transferring dockerf 605B  0.0s
 => [internal] load metadata for  0.5s
 => [internal] load .dockerignor  0.1s
 => => transferring context: 2B   0.0s
 => [1/8] FROM docker.io/ethereu  0.0s
 => [internal] load build contex  0.0s
 => => transferring conte 1.20kB  0.0s
 => CACHED [2/8] WORKDIR /root    0.0s
 => CACHED [3/8] COPY ./genesis.  0.0s
 => CACHED [4/8] COPY ./password  0.0s
 => [5/8] COPY ./start-geth.sh /  0.1s
 => [6/8] RUN chmod +x /root/sta  0.5s
 => [7/8] RUN mkdir -p /root/dat  0.5s
 => [8/8] RUN geth --datadir=/ro  0.8s
 => exporting to image            0.3s
 => => exporting layers           0.2s
 => => writing image sha256:bb89  0.0s
 => => naming to docker.io/libra  0.0s
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/docker/blockchain$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ docker run -d -p 8545:8545 -p 8546:8546 -p 30303:30303 -p 30303:30303/udp --name hospital-blockchain hospital-blockchain
7b895edf3921a67a1cf4c3e27fb858805e420e6cdc7dd6beea41fbccfce192dd
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ docker logs hospital-blockchain
Creating new account...
INFO [03-09|22:55:24.911] Maximum peer count                       ETH=50 LES=0 total=50
INFO [03-09|22:55:24.912] Smartcard socket not found, disabling    err="stat /run/pcscd/pcscd.comm: no such file or directory"
Using account: 0x7644076314a6273080993C206F2536A5505C0D7B
INFO [03-09|22:55:28.810] Maximum peer count                       ETH=50 LES=0 total=50
INFO [03-09|22:55:28.812] Smartcard socket not found, disabling    err="stat /run/pcscd/pcscd.comm: no such file or directory"
WARN [03-09|22:55:28.813] Option nousb is deprecated and USB is deactivated by default. Use --usb to enable 
INFO [03-09|22:55:28.820] Set global gas cap                       cap=50,000,000
INFO [03-09|22:55:28.822] Allocated trie memory caches             clean=154.00MiB dirty=256.00MiB
INFO [03-09|22:55:28.824] Using leveldb as the backing database 
INFO [03-09|22:55:28.826] Allocated cache and file handles         database=/root/data/geth/chaindata cache=512.00MiB handles=524,288
INFO [03-09|22:55:28.868] Using LevelDB as the backing database 
INFO [03-09|22:55:28.949] Opened ancient database                  database=/root/data/geth/chaindata/ancient/chain readonly=false
INFO [03-09|22:55:28.952] Disk storage enabled for ethash caches   dir=/root/data/geth/ethash count=3
INFO [03-09|22:55:28.952] Disk storage enabled for ethash DAGs     dir=/root/.ethash          count=2
INFO [03-09|22:55:28.953] Initialising Ethereum protocol           network=1337 dbversion=<nil>
INFO [03-09|22:55:28.954]  
INFO [03-09|22:55:28.955] --------------------------------------------------------------------------------------------------------------------------------------------------------- 
INFO [03-09|22:55:28.955] Chain ID:  1337 (unknown) 
INFO [03-09|22:55:28.955] Consensus: unknown 
INFO [03-09|22:55:28.955]  
INFO [03-09|22:55:28.955] Pre-Merge hard forks (block based): 
INFO [03-09|22:55:28.955]  - Homestead:                   #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/homestead.md) 
INFO [03-09|22:55:28.955]  - Tangerine Whistle (EIP 150): #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/tangerine-whistle.md) 
INFO [03-09|22:55:28.955]  - Spurious Dragon/1 (EIP 155): #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/spurious-dragon.md) 
INFO [03-09|22:55:28.955]  - Spurious Dragon/2 (EIP 158): #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/spurious-dragon.md) 
INFO [03-09|22:55:28.955]  - Byzantium:                   #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/byzantium.md) 
INFO [03-09|22:55:28.955]  - Constantinople:              #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/constantinople.md) 
INFO [03-09|22:55:28.955]  - Petersburg:                  #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/petersburg.md) 
INFO [03-09|22:55:28.955]  - Istanbul:                    #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/istanbul.md) 
INFO [03-09|22:55:28.955]  - Berlin:                      #<nil> (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md) 
INFO [03-09|22:55:28.955]  - London:                      #<nil> (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london.md) 
INFO [03-09|22:55:28.955]  
INFO [03-09|22:55:28.955] The Merge is not yet available for this network! 
INFO [03-09|22:55:28.955]  - Hard-fork specification: https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/paris.md 
INFO [03-09|22:55:28.955]  
INFO [03-09|22:55:28.955] Post-Merge hard forks (timestamp based): 
INFO [03-09|22:55:28.955]  
INFO [03-09|22:55:28.955] --------------------------------------------------------------------------------------------------------------------------------------------------------- 
INFO [03-09|22:55:28.955]  
INFO [03-09|22:55:28.956] Loaded most recent local block           number=0 hash=0ab943..0839a3 td=1024 age=55y11mo3w
WARN [03-09|22:55:28.958] Failed to load snapshot                  err="missing or corrupted snapshot"
INFO [03-09|22:55:28.958] Rebuilding state snapshot 
INFO [03-09|22:55:28.958] Resuming state snapshot generation       root=4e0d67..7f60c1 accounts=0 slots=0 storage=0.00B dangling=0 elapsed="304.225µs"
INFO [03-09|22:55:28.959] Generated state snapshot                 accounts=1 slots=0 storage=51.00B dangling=0 elapsed="742.95µs"
INFO [03-09|22:55:28.959] Regenerated local transaction journal    transactions=0 accounts=0
INFO [03-09|22:55:28.962] Gasprice oracle is ignoring threshold set threshold=2
WARN [03-09|22:55:28.963] Error reading unclean shutdown markers   error="leveldb: not found"
WARN [03-09|22:55:28.963] Engine API enabled                       protocol=eth
WARN [03-09|22:55:28.964] Engine API started but chain not configured for merge yet 
INFO [03-09|22:55:28.965] Starting peer-to-peer node               instance=Geth/v1.11.6-stable-ea9e62ca/linux-amd64/go1.20.3
INFO [03-09|22:55:29.012] New local node record                    seq=1,741,560,929,011 id=8905d924b72f606c ip=127.0.0.1 udp=0 tcp=30303
INFO [03-09|22:55:29.012] Started P2P networking                   self="enode://5f40ad1499a6176fdff69c890ccdbe783b27b6a0bce9248a58c3952d4e0f760b5c46a8c5965ee85904b723a72ee5f1e0582a42c36c5fad63f4e3b8baf61eb75b@127.0.0.1:30303?discport=0"
INFO [03-09|22:55:29.013] IPC endpoint opened                      url=/root/data/geth.ipc
INFO [03-09|22:55:29.014] Generated JWT secret                     path=/root/data/geth/jwtsecret
INFO [03-09|22:55:29.015] HTTP server started                      endpoint=[::]:8545 auth=false prefix= cors=* vhosts=localhost
INFO [03-09|22:55:29.015] WebSocket enabled                        url=ws://[::]:8546
INFO [03-09|22:55:29.016] WebSocket enabled                        url=ws://127.0.0.1:8551
INFO [03-09|22:55:29.016] HTTP server started                      endpoint=127.0.0.1:8551 auth=true  prefix= cors=localhost vhosts=localhost
INFO [03-09|22:55:30.349] Unlocked account                         address=0x7644076314a6273080993C206F2536A5505C0D7B
INFO [03-09|22:55:30.350] Transaction pool price threshold updated price=0
INFO [03-09|22:55:30.350] Updated mining threads                   threads=1
INFO [03-09|22:55:30.350] Transaction pool price threshold updated price=1,000,000,000
INFO [03-09|22:55:30.350] Commit new sealing work                  number=1 sealhash=d3cf70..f7b496 uncles=0 txs=0 gas=0 fees=0 elapsed="293.064µs"
INFO [03-09|22:55:30.350] Commit new sealing work                  number=1 sealhash=d3cf70..f7b496 uncles=0 txs=0 gas=0 fees=0 elapsed="444.048µs"
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ 
I see that the project is quite large, with several directories contributing to the size:

hospital-management-system-master: 4.2GB
rust-secure-db: 1.6GB
medical-chain: 453MB
venv: 141MB

esh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/hospital-management-system-master$ npm install keycloak-js --save

added 1 package, and audited 2291 packages in 26s

324 packages are looking for funding
  run `npm fund` for details

62 vulnerabilities (3 low, 38 moderate, 21 high)

To address issues that do not require attention, run:
  npm audit fix

To address all issues possible (including breaking changes), run:
  npm audit fix --force

Some issues need review, and may require choosing
a different dependency.

Run `npm audit` for details.
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main/hospital-management-system-master$ cd '/home/umesh/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main'
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ du -sh *
4.0K    =1.2.0
8.0K    DEPLOYMENT.md
4.0K    deploy.sh
4.0K    DEVELOPER_MODE.md
4.0K    devmode.md
8.0K    dev-mode.py
44K     docker
8.0K    docker-compose.yml
56K     docs
4.0K    environment_variables.txt
804K    flask-backend
4.0K    get_env_python.py
4.2G    hospital-management-system-master
284K    integration-tests
124K    k8s
453M    medical-chain
32K     nginx
8.0K    postgres
3.5M    PRODUCT MOCKUP GLOBAL SUSHRUT.pdf
16K     project_info.txt
8.0K    README.md
4.0K    requirements-clean.txt
4.0K    requirements-essential.txt
4.0K    requirements-final.txt
4.0K    requirements-new.txt
12K     requirements.txt
1.6G    rust-secure-db
20K     testing.md
32K     test.md
141M    venv
20K     verification
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hospital-management-system-master/.env
        modified:   hospital-management-system-master/package-lock.json
        modified:   hospital-management-system-master/package.json
        modified:   hospital-management-system-master/src/components/DetailsForm/MarketplaceForm.jsx
        modified:   hospital-management-system-master/src/components/Marketplace/Marketplace.jsx
        modified:   hospital-management-system-master/src/components/User/EditUser.jsx
        modified:   hospital-management-system-master/src/services/KeycloakService.jsx
        modified:   test.md

no changes added to commit (use "git add" and/or "git commit -a")
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ git lfs install
git: 'lfs' is not a git command. See 'git --help'.

The most similar command is
        refs
umesh@umesh-Latitude-E5470:~/Videos/Hopital-Management-System-master-main(3)/Hopital-Management-System-master-main$ sudo apt-get install git-lfs
[sudo] password for umesh: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  git-lfs
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 3,316 kB of archives.
After this operation, 11.1 MB of additional disk space will be used.
Get:1 http://ca.archive.ubuntu.com/ubuntu focal/universe amd64 git-lfs amd64 2.9.2-1 [3,316 kB]
Fetched 3,316 kB in 6s (546 kB/s)                                       
debconf: unable to initialize frontend: Dialog
debconf: (Dialog frontend requires a screen at least 13 lines tall and 31 columns wide.)
debconf: falling back to frontend: Readline
Selecting previously unselected package git-lfs.
(Reading database ... 225366 files and directories currently installed.)
Preparing to unpack .../git-lfs_2.9.2-1_amd64.deb ...
Unpacking git-lfs (2.9.2-1) ...
Setting up git-lfs (2.9.2-1) ...
Processing triggers for man-db (2.9.1-1) ...