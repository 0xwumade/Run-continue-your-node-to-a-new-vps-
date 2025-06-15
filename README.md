# Run-continue-your-node-to-a-new-vps-

🔁 Move Aztec Node to a New VPS


---

✅ 1. On Your Old VPS: Backup the Node Data

If you used Docker:

Your critical files are likely in:

Docker volumes (/var/lib/docker/volumes/)

Or mounted folders (e.g., aztec/data, .aztec, .keys, .env, etc.)


Most important things to backup:

.aztec/ folder (contains prover identity and database)

.env file (contains config, RPC endpoints, etc.)

Any custom keys used for sequencer or prover

yarn-project/ or aztec-packages/ if cloned locally

Docker volumes if you didn’t mount external folders


🔄 Use rsync to transfer:

rsync -avz ~/.aztec user@NEW_VPS_IP:~/
rsync -avz ~/aztec-packages user@NEW_VPS_IP:~/
rsync -avz ~/.env user@NEW_VPS_IP:~/


---

✅ 2. On New VPS: Set Up Environment

Basic Setup:

sudo apt update && sudo apt upgrade -y
sudo apt install git curl build-essential docker.io docker-compose -y

Install Node & Yarn (if not using Docker entirely):

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
npm install -g yarn


---

✅ 3. Clone & Install Aztec Packages

If you use the monorepo (Aztec v2 or v3):

git clone https://github.com/AztecProtocol/aztec-packages.git
cd aztec-packages
yarn install

Or clone your own fork if you modified it.


---

✅ 4. Restore Config Files

Copy your old .env and .aztec:

scp user@OLD_VPS_IP:~/.env ~/.env
scp -r user@OLD_VPS_IP:~/.aztec ~/.aztec

Make sure your .env has correct values:

Geth/Prysm RPC endpoints (or use Aztec L1 fork RPC)

Archive/Prover/Sequencer configs



---

✅ 5. Start the Node

Depending on what you're running (prover, sequencer, etc):

🔹 To start a prover:

yarn workspace @aztec/prover start --archive --historical

🔹 To start a sequencer:

yarn workspace @aztec/sequencer start

> If using Docker, ensure the correct volumes and ports are restored.




---

✅ 6. Verify It's Working

Check logs:

tail -f ~/.aztec/logs/*.log

Or use:

docker logs -f <container_name>

Make sure:

Sequencer is requesting blocks

Prover is generating proofs

It connects to the correct L1 nodes (e.g., Geth, Prysm)

No errors like "identity not found", "no peers", or "db lock"



---

🧠 Extra Tips

Firewall: Open only the necessary ports (usually none unless using custom RPC).

Keys: Your .aztec folder contains prover/sequencer identities — DO NOT delete.

Docker users: You can docker save and docker load images if you want to move them too.

Snapshot: Aztec node currently doesn’t support L2 snapshots, so keeping the prover DB helps resume faster.
