# Gevulot Devnet
 >Gevulot devnet - arbitrary provers and verifiers
 >launching date 25 March 2024.

![2e4dad6d-3bbf-4b15-8d05-e7d8464f6c99_1500x500](https://github.com/papadritta/Gevulot_my/assets/90826754/e10c4c4d-1085-431d-8f7b-b3b4d508e5d7)

#### Node requirements for devnet
```
CPU: 64+ cores
RAM: 512GB
Disk: 1 TB NVMe
```
#### Oficial links:

Gevulot Github [repo](https://github.com/gevulotnetwork/gevulot/blob/main/INSTALL.md)

Gevulot [docks](https://docs.gevulot.com/gevulot-docs)

Gevulot [website](https://gevulot.com/)

#### Devnet form sudmission

[Here](https://airtable.com/appS1ebiXFs8H4OP5/pagVuySwNkMe95tIi/form)

#### Installation:

#### 1. Install Podman
```
cd $HOME
sudo apt update
sudo apt install pkg-config libssl-dev podman cargo protobuf-compiler
```
#### 2. Clone the Gevulot repo
```
git clone https://github.com/gevulotnetwork/gevulot.git
```
#### 3. Make a dirs
```
cd /root/gevulot
mkdir -p data/{db,node}
```
#### 4. Set up PostgreS
```
podman run -d --rm --name gevulot-postgres --network host -v ./crates/node/migrations:/docker-entrypoint-initdb.d:z -v ./data/db:/var/lib/postgres/data:z --env POSTGRES_USER=gevulot --env POSTGRES_PASSWORD=gevulot --env POSTGRES_DB=gevulot docker.io/library/postgres:16-alpine
```

#### 5. Compile the release
```
cargo build --release
```

#### 6. Generate a public node key
```
./target/release/gevulot generate key --key-file data/node/node.key
```
>it generate a Public key: Key generated and saved in file data/node/node.key

#### 7. Enable module for virtual socket communication
```
sudo modprobe vhost_vsock
```
#### 8. Run the Gevulot node
```
./target/release/gevulot run --data-directory ./data/node --log-directory ./data/node/log --node-key-file ./data/node/node.key
```
#### 9. Install Ops 

```
curl https://ops.city/get.sh -sSfL | sh
```
#### 10. Generate keys (new cryptographic key pair)
```
cd gevulot
./target/release/gevulot-cli generate-key
```
>it's generate pubkey <pubkey1> and saved in file:localkey.pki

#### 11. Whitelist the public key (add your <pubkey1> from above)
```
./target/release/gevulot peer <pubkey> whitelist
```
#### 12. Display the public key of your node:
```
./target/release/gevulot show public-key --key-file data/node/node.key
```
> it will display <pubkey2> 

#### 13. Add this key <pubkey2> to the whitelist:
```
./target/release/gevulot peer <pubkey2> whitelist
```
#### 14. Navigate to E2E tests directory:
```
cd crates/tests/e2e-tests/
```
#### 15. Copy the Prover and Verifier to the test directory:
```
cp ../../../target/release/{prover,verifier} .
```
#### 16. Create the Prover image with Ops:
```
ops image create -n -c manifest/prover.json
```
#### 17. Create the Verifier image with Ops:
```
ops image create -n -c manifest/verifier.json
```
#### 18. Run the end-to-end test:
```
cargo run -- -p ~/.ops/images/prover -v ~/.ops/images/verifier -k ../../../localkey.pki
```
>the tests were successful:
```
...
Listening on <your ip address>
request file: 51a68fd499fa25fc5aa5abb5c9a1cb2e2060831c8c6df96c1196a277253865e6
request file: c47255b3c219c4fd7bdb72e2080d5d2b46ac297e4c52bb0ec26b6ffe0e376e90
```






















 