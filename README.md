# aztec-node-termux-guide
Aztec sequencer node setup using Alpine Linux + QEMU inside Termux (No root)

# Aztec Sequencer Node on Termux (Alpine + QEMU)

Run an Aztec Sequencer Node directly on **Termux**, using **QEMU + Alpine Linux**, with Docker installed inside Alpine.

---

## Step 1: Install Packages & Download Alpine ISO

```bash
pkg update -y && pkg upgrade -y
pkg install -y git wget curl qemu-utils qemu-system-x86_64-headless

wget https://dl-cdn.alpinelinux.org/alpine/v3.19/releases/x86_64/alpine-standard-3.19.1-x86_64.iso -O alpine.iso

qemu-img create -f qcow2 alpine.img 32G

qemu-system-x86_64 -m 1024 -cdrom alpine.iso -hda alpine.img -net nic -net user -nographic
```

---

## Step 2: Inside Alpine Setup (as root)

**Login as root**  
Add Alpine repositories:

```bash
echo -e "http://dl-cdn.alpinelinux.org/alpine/v3.19/main\nhttp://dl-cdn.alpinelinux.org/alpine/v3.19/community" > /etc/apk/repositories
echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
```

Run interactive setup:

```bash
setup-alpine
```

> **Key prompts to follow:**
> - Select or type `sys`
> - Select or type `sda`
> - Set root password
> - Press Enter to clear disk
> - Press Enter for all other prompts

After installation, power off:

```bash
poweroff
```

Wait for 2 minutes.

---

## Step 3: Boot into Installed Alpine Image

```bash
qemu-system-x86_64 -m 1024 -hda alpine.img -net nic -net user -nographic
```

Update & install essential tools:

```bash
echo "http://dl-cdn.alpinelinux.org/alpine/v3.19/community" >> /etc/apk/repositories

apk update
apk add bash
apk add curl
apk add wget
apk add screen
apk add jq
apk add docker
apk add docker-cli-compose
```

Start Docker & test:

```bash
rc-service docker start

docker --version
docker run hello-world
docker compose version
```

---

## Step 4: Start Aztec Node

```bash
[ -f "aztec.sh" ] && rm aztec.sh
curl -sSL -o aztec.sh https://raw.githubusercontent.com/zunxbt/aztec-sequencer-node/main/aztec.sh
chmod +x aztec.sh
./aztec.sh
```

> **You'll be prompted to input:**
> - RPC URL
> - Beacon URL
> - Private key (with at least 2.5 Sepolia ETH)
> - Wallet address

---

## Step 5: Get Block Number

```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r '.result.proven.number'
```

> After getting the block number, insert it into this command:

```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[<BLOCK_NUMBER>],"id":67}' \
http://localhost:8080
```

---

## Step 6: Get Apprentice Role on Aztec Discord

1. Join the [Aztec Discord server](https://discord.gg/aztec)
2. Go to the `#operators | start-here` channel
3. Type the command:

```
/operator start
```

4. Provide:
   - Your wallet address
   - The block number
   - The proof from the previous step

You’ll get the **Apprentice** role instantly.

---

## Disclaimer

- You need a **reliable RPC and Beacon URL**
- Your device must be **midrange to flagship**, and in good condition
- Wallet must have at least **2.5 Sepolia ETH**
- This guide is for **educational purposes only**
- A **strong and stable internet connection** is required

---

## Credit

- Compiled by [@admirkhen](https://x.com/admirkhen)
- Shoutout: [@zun2025](https://github.com/zunxbt), [@bigray0x](https://x.com/bigray0x)
