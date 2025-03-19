# BDK Wallet Exercise: Building a Testnet Wallet Module

Welcome to the **BDK Wallet Exercise**, a hands-on component of *Advanced Bitcoin Development with Core & Libraries*. This exercise leverages the Bitcoin Development Kit (BDK) to construct a functional wallet module, utilizing its HD wallet capabilities (BIP32/84), SPV blockchain synchronization, and descriptor-based scripts. You’ll implement this in either Python (via `bdkpython`) or JavaScript (via `@bitcoinerlab/bdk`), targeting Bitcoin’s testnet. The goal is to deepen your understanding of BDK’s application in real-world wallet development while engaging with live blockchain data.

## Objectives
- Initialize an HD wallet with a mnemonic seed and BIP84 (SegWit) derivation.
- Generate receiving addresses and sync wallet state with testnet using SPV.
- Retrieve and display the wallet’s balance, interacting with live testnet transactions.
- Explore BDK’s descriptor-based approach to script management.

## Prerequisites
- **Python**: Version 3.8+ with `pip`.
- **JavaScript**: Node.js 16+ with `npm`.
- **Network**: Access to a testnet Electrum server (e.g., `ssl://electrum.blockstream.info:60002`).
- **Tools**: Git for repository management.

## Exercise Workflow

### Step 1: Environment Setup
Configure your development environment to use BDK.

#### Python
- Install dependencies: `pip install bdkpython` (includes `bdkpython`).
- Verify: Ensure `wallet.py` is present in the repository.

#### JavaScript
- Install dependencies: `npm install` (installs `@bitcoinerlab/bdk` per `package.json`).
- Verify: Ensure `wallet.js` is present.

Execute from the repository root:
```bash
# Python
pip install bdkpython
# JavaScript
npm install
```

## Step 2: Initialize the Wallet with a Mnemonic
Generate a 12-word mnemonic seed to seed your HD wallet.

### Starter Code (Provided)

#### Python (`wallet.py`):
```python
import bdkpython as bdk

print("Generating mnemonic seed...")
mnemonic = bdk.Mnemonic(word_count=12)
print(f"Mnemonic: {mnemonic}")
```

#### JavaScript (`wallet.js`):
```javascript
const { mnemonic } = require('@bitcoinerlab/bdk');
console.log("Generating mnemonic seed...");
const mn = mnemonic.generate(12);
console.log(`Mnemonic: ${mn}`);
```

### Execution:
- **Python:** `python wallet.py`
- **JavaScript:** `node wallet.js`

### Output:
A 12-word mnemonic (e.g., "abandon ability ...").

> **Note:** Securely store this mnemonic; it’s the key to your wallet’s private keys.

---

## Step 3: Generate Testnet Addresses
Derive three SegWit (BIP84) addresses for receiving testnet funds.

### Starter Code (Provided)

#### Python:
```python
descriptor = bdk.Descriptor(f"wpkh({mnemonic.to_seed_normalized('')[0:64]}/84'/1'/0'/0/*)", bdk.Network.TESTNET)
change_descriptor = bdk.Descriptor(f"wpkh({mnemonic.to_seed_normalized('')[0:64]}/84'/1'/0'/1/*)", bdk.Network.TESTNET)
wallet = bdk.Wallet(descriptor, change_descriptor, bdk.Network.TESTNET, bdk.MemoryDatabase())

print("\nGenerated Addresses:")
for i in range(3):
    address = wallet.get_address(bdk.AddressIndex.NEW).address
    print(f"Address {i+1}: {address}")
```

#### JavaScript:
```javascript
const { Descriptor, Wallet, Network, MemoryDatabase } = require('@bitcoinerlab/bdk');
const descriptor = new Descriptor(`wpkh(${mn.toSeed('').slice(0, 64)}/84'/1'/0'/0/*)`, Network.TESTNET);
const changeDescriptor = new Descriptor(`wpkh(${mn.toSeed('').slice(0, 64)}/84'/1'/0'/1/*)`, Network.TESTNET);
const wallet = new Wallet(descriptor, changeDescriptor, Network.TESTNET, new MemoryDatabase());

console.log("\nGenerated Addresses:");
for (let i = 0; i < 3; i++) {
    const address = wallet.getAddress('new').address;
    console.log(`Address ${i+1}: ${address}`);
}
```

### Execution:
Run as in Step 2.

### Output:
Three testnet SegWit addresses (e.g., `tb1q...`).

> **Note:** These addresses follow BIP84 (`m/84'/1'/0'`), optimized for testnet.

---

## Step 4: Sync with Testnet
Implement blockchain synchronization to retrieve the wallet’s balance.

### Task
Add code to connect to an Electrum server and sync the wallet state:

#### Python:
```python
blockchain = bdk.ElectrumBlockchain(config={"url": "ssl://electrum.blockstream.info:60002"})
print("\nSynchronizing with testnet...")
wallet.sync(blockchain)
balance = wallet.get_balance()
print(f"Balance: {balance.total} satoshis ({balance.total / 100_000_000:.8f} BTC)")
```

#### JavaScript:
```javascript
const { ElectrumBlockchain } = require('@bitcoinerlab/bdk');
const blockchain = new ElectrumBlockchain({ url: "ssl://electrum.blockstream.info:60002" });
console.log("\nSynchronizing with testnet...");
wallet.sync(blockchain);
const balance = wallet.getBalance();
console.log(`Balance: ${balance.total} satoshis (${balance.total / 100000000} BTC)`);
```

### Execution:
Re-run the script after adding this code.

### Output:
Initial balance (likely `0 satoshis` until funded).

> **Note:** Uses SPV for lightweight synchronization; no full node required.

---

## Step 5: Fund the Wallet
Simulate real-world usage by funding your wallet with testnet BTC.

### Instructions
1. Select one of your generated addresses.
2. Request testnet coins from a faucet (e.g., testnet-faucet.com) or a peer.
3. Wait briefly (1-2 minutes) for the transaction to propagate.
4. Re-run the script to update the balance.

### Expected Output:
Updated balance (e.g., “Balance: 50000 satoshis”).

> **Note:** Testnet faucets may have rate limits; coordinate with peers if needed.

---

## Step 6: Verify Funding Status
Add logic to confirm the wallet has been funded.

### Task
Append a status check after the balance:

#### Python:
```python
if balance.total > 0:
    print("Funding confirmed—wallet is active.")
else:
    print("No funds detected—continue funding.")
```

#### JavaScript:
```javascript
if (balance.total > 0) {
    console.log("Funding confirmed—wallet is active.");
} else {
    console.log("No funds detected—continue funding.");
}
```

### Execution:
Re-run after funding.

### Output:
Confirmation message based on balance.

---

## Optional Extensions
- **Transaction Building:** Implement a send function using `wallet.buildTx()` to transfer funds.
- **Alternative Derivation:** Experiment with BIP44 (P2PKH) by modifying the descriptor (e.g., `pkh(...)`).
- **Transaction History:** Retrieve transaction details with `wallet.listTransactions()`.

---

## Submission
1. Commit your work:
   ```sh
   git add . && git commit -m "Completed BDK wallet exercise" && git push
   ```
2. Verify functionality in class or via a pull request.

---

## Resources
- **BDK Documentation:** [BDK](https://bitcoindevkit.org/docs/)
- **Testnet Faucet:** [testnet-faucet.com](https://testnet-faucet.com)
- **Support:** Consult the instructor or reference BDK’s API docs.

