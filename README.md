# Docker-Subspace

Minimal Spesifikasi VPS untuk multipel node

8CORE VCPU

16GB RAM

500GB Storage

Kalau 4 VCPU 8GB RAM 200GB Storage bisa cuman 1 Node Docker dan gak boleh di gabung sama apa" lagi kalau tidak akan terjadi error 

Install Docker 
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc ack tmux ncdu -y
sudo apt-get install ca-certificates curl gnupg lsb-release -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```

Install Docker Compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Folder Node
```
cd $HOME
mkdir node1
cd node1
```

Install script Subspace
```
sudo tee docker-compose.yml > /dev/null <<EOF
version: "3.7"
services:
  node:
    image: ghcr.io/subspace/node:gemini-2a-2022-sep-10
    volumes:
      - node-data:/var/subspace:rw
    ports:
      - "0.0.0.0:11333:11333"
    restart: unless-stopped
    command: [
      "--chain", "gemini-2a",
      "--base-path", "/var/subspace",
      "--execution", "wasm",
      "--state-pruning", "archive",
      "--port", "11333",
      "--rpc-cors", "all",
      "--rpc-methods", "safe",
      "--unsafe-ws-external",
      "--validator",
      "--name", "NAMANODE"
    ]
    healthcheck:
      timeout: 5s
      interval: 30s
      retries: 5

  farmer:
    depends_on:
      node:
        condition: service_healthy
    image: ghcr.io/subspace/farmer:gemini-2a-2022-sep-10
    volumes:
      - farmer-data:/var/subspace:rw
    ports:
      - "0.0.0.0:22333:22333"
    restart: unless-stopped
    command: [
      "--base-path", "/var/subspace",
      "farm",
      "--node-rpc-url", "ws://node:9944",
      "--ws-server-listen-addr", "0.0.0.0:9955",
      "--listen-on", "/ip4/0.0.0.0/tcp/22333",
      "--reward-address", "ADDRESSREWARD",
      "--plot-size", "30G"
    ]
volumes:
  node-data:
  farmer-data:
EOF
```

Edit Script
ubah bagian <strong>NAMANODE</strong> dan <strong>ADDRESSREWARD</strong> kalau sudah Ctrl+X trus Y dan Enter tanda "" jangan dihapus yah
```
nano docker-compose.yml
```

Jalankan Script
```
docker-compose up -d
```

Cek Log
```
docker-compose logs --tail=1000 --follow
```

## <strong>Tutorial untuk Nuyul lebih dari 1 </strong>

angkat dibelakang tulisan <strong>node</strong> dirubah jika mau lebih dari 2
```
cd $HOME
mkdir node2
cd node2
```

Masukkan Script 
```
sudo tee docker-compose.yml > /dev/null <<EOF
version: "3.7"
services:
  node:
    image: ghcr.io/subspace/node:gemini-2a-2022-sep-10
    volumes:
      - node-data:/var/subspace:rw
    ports:
      - "0.0.0.0:PORT:PORT"
    restart: unless-stopped
    command: [
      "--chain", "gemini-2a",
      "--base-path", "/var/subspace",
      "--execution", "wasm",
      "--state-pruning", "archive",
      "--port", "PORT",
      "--rpc-cors", "all",
      "--rpc-methods", "safe",
      "--unsafe-ws-external",
      "--validator",
      "--name", "NAMANODE"
    ]
    healthcheck:
      timeout: 5s
      interval: 30s
      retries: 5

  farmer:
    depends_on:
      node:
        condition: service_healthy
    image: ghcr.io/subspace/farmer:gemini-2a-2022-sep-10
    volumes:
      - farmer-data:/var/subspace:rw
    ports:
      - "0.0.0.0:PORT:PORT"
    restart: unless-stopped
    command: [
      "--base-path", "/var/subspace",
      "farm",
      "--node-rpc-url", "ws://node:9944",
      "--ws-server-listen-addr", "0.0.0.0:9955",
      "--listen-on", "/ip4/0.0.0.0/tcp/PORT",
      "--reward-address", "ADDRESSREWARD",
      "--plot-size", "30G"
    ]
volumes:
  node-data:
  farmer-data:
EOF
```

Ubah bagian <strong>PORT</strong>, <strong>NAMANODE</strong>, dan <strong>ADDRESSREWARD</strong> tanda "" jangan dihapus yah
```
nano docker-compose.yml
```

Jalankan Script
```
docker-compose up -d
```

Cek Log
```
docker-compose logs --tail=1000 --follow
```

Delete Node di docker
```
docker-compose down --volumes
```

## Terima kasih
