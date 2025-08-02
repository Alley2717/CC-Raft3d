# Raft3D – Distributed 3D Printer Management using Raft (Go)

This project implements a backend system to manage 3D printers, filaments, and print jobs using the Raft consensus algorithm for distributed consistency and fault tolerance.

---

## Features

- Leader election & log replication using HashiCorp's Raft
- REST API for managing printers, filaments, and print jobs
- Raft FSM for applying consistent state changes across nodes
- Periodic snapshots for crash recovery
- Leader redirection & failover support

---

## Tech Stack

- **Language:** Go
- **Consensus Library:** [HashiCorp/raft](https://github.com/hashicorp/raft)
- **UUIDs:** github.com/google/uuid

---

## Setup Instructions

### 1. Clone the repo
git clone https://github.com/alley2717/raft3d.git
cd raft3d 

### 2. Install dependencies
go mod tidy 

## Launching the Cluster
Open three separate terminal windows to run each node.

---

### Node 1 (Initial Leader)
go run main.go fsm.go --id node1 --raft-port 12000 --http-port 8080
### Node 2 (Join Node):
go run main.go fsm.go --id node2 --raft-port 12001 --http-port 8081 --join 127.0.0.1:8080
### Node 3 (Join Node):
go run main.go fsm.go --id node3 --raft-port 12002 --http-port 8082 --join 127.0.0.1:8080

---

## API Endpoints (use Postman or curl)
### Printers
POST /api/v1/printers - Adds a new 3D printer.

GET /api/v1/printers - Lists all registered printers.

### Filaments
POST /api/v1/filaments - Adds a new filament spool.

GET /api/v1/filaments - Lists all available filaments.

### Print Jobs
POST /api/v1/print_jobs - Creates a new print job.

GET /api/v1/print_jobs?status=<status> - Lists print jobs by status (e.g., Queued, Running).

POST /api/v1/print_jobs/<id>/status?status=<new_status> - Updates the status of a specific print job (e.g., Running, Done, Canceled).

### Raft Cluster Management
GET /raft/state - Shows the node's current role (Leader/Follower) and the address of the leader.

GET /join?peerID=<nodeID>&peerAddr=<ip:port> - Manually requests a node to join the cluster.

---

## Demo Checklist
- Start 3 nodes as above
- Use Postman to:
- Add printer, filament, print job
- Transition job status from Queued → Running → Done
- Verify filament weight reduces correctly
- Kill the leader and observe leader re-election
- Restart old node and confirm state sync via snapshot/log replay


