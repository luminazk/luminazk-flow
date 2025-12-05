# 💡 LuminaZK: Private & Trustless P2P Data Exchange on Solana

[Tambahkan Badge seperti: Build Status, License, Solana Compatibility, dll.]

## ✨ Summary: Verify Everything, Reveal Nothing

**LuminaZK** is a pioneering **Solana-based protocol** that redefines digital commerce by enabling truly private and trustless Peer-to-Peer (P2P) data exchange. Leveraging cutting-edge **Zero-Knowledge (ZK) privacy** technology, LuminaZK allows users to buy and sell digital content (files, art, datasets, etc.) without revealing the content or relying on central intermediaries.

Our core commitment is to verifiable privacy: sellers can generate **ZK-Attestations** that cryptographically prove properties of their content (e.g., file type, size, or successful encryption) without disclosing the content itself. This builds **on-chain trust** where verification precedes access, all executed with zero data leaks and no additional platform fees.

---

## 🛠️ Technology Stack

| Component | Role | Technology |
| :--- | :--- | :--- |
| **Blockchain** | High-Throughput Settlement | **Solana** |
| **Cryptography** | Privacy & Verification | **ZK-SNARKs** (e.g., Marlin/PlonK) |
| **Encryption** | Content Protection | **Poseidon Cipher** |
| **Key Exchange** | Secure Session Establishment | **Elliptic Curve Diffie-Hellman (ECDH)** |
| **Storage** | Decentralized Content Hosting | **IPFS (InterPlanetary File System)** |
| **Smart Contract** | Protocol Logic & Proof Verification | **Rust/Anchor Framework** |

---

## 🔑 Core Features & Use Cases

### 1. **Core Features**

* **Verifiable Privacy:** Content is secured using **Poseidon Encryption**, optimized for ZK circuits.
* **Trustless ZK-Attestations:** Sellers generate ZK proofs validating the encryption and content integrity.
* **P2P & Fee-Free:** Direct exchange between parties; the protocol takes no cut.
* **Decentralized Storage:** Content is stored robustly on **IPFS**.

### 2. **Use Cases**

* **Private Data Marketplaces:** Selling sensitive datasets (e.g., financial models, medical data) where the buyer needs verification (e.g., schema matches) but not the actual data upfront.
* **Secure Digital Art/Media Licensing:** Licensing high-value digital media where the licensee needs proof of authenticity/ownership property without obtaining the high-resolution source until payment.
* **Software/Algorithm Distribution:** Selling proprietary software algorithms where the seller proves the code meets certain performance metrics without revealing the source code.
* **Verifiable Education/Certification:** Distributing verifiable academic certificates or licenses where the authenticity is proven via ZK without needing the issuer's direct involvement in every verification.

---

## 📝 User Flow

This flow describes the steps from a Seller listing content to a Buyer successfully receiving the decrypted file.

1.  **Seller Preparation:**
    * The Seller prepares the digital content.
    * The content is split into **chunks** for efficient storage and encryption.
2.  **Encryption & Storage:**
    * The Seller encrypts each chunk using the **Poseidon Cipher** and a key derived via the intended Buyer's public key (or a temporary shared secret).
    * The Seller pins the resulting encrypted content **blob** to **IPFS** and receives the CID (Content Identifier).
3.  **Proof Generation (Off-Chain):**
    * The Seller runs the ZK circuit prover, generating a **ZK-SNARK proof** that attests to the correct, verifiable encryption of the chunks and the commitment to the unencrypted content hash.
4.  **Listing Creation (On-Chain):**
    * The Seller deploys a new listing Smart Contract on Solana, including:
        * The price in $LUMZ token (LuminaZK native token).
        * The **IPFS CID** of the encrypted data.
        * The generated **ZK-SNARK proof**.
        * The public key commitment for the **ECDH** exchange.
5.  **Buyer Verification & Purchase:**
    * The Buyer finds the listing and first verifies the **ZK-SNARK proof** against the listing contract parameters. ****
    * If the proof is valid (verifiable privacy confirmed), the Buyer sends the payment ($LUMZ) to the contract.
6.  **Key Delivery & Decryption:**
    * Upon successful payment, the contract releases the necessary cryptographic key information (derived via **ECDH**) to the Buyer.
    * The Buyer fetches the encrypted blob from **IPFS** using the CID and uses the received key to decrypt the content, completing the trustless P2P exchange.

---

## ⚙️ Mechanism Flow: Cryptographic Workflow

The integrity of LuminaZK relies on the sequential and verifiable interaction of its cryptographic components:

### 1. Secure Key Establishment (ECDH)

* The Seller and Buyer exchange public keys.
* Both parties compute a unique **shared secret** using the Elliptic Curve Diffie-Hellman (ECDH) algorithm.
* A **Session Key** is derived from this shared secret, used only for the current content encryption/decryption.

### 2. Poseidon Encryption and IPFS Storage

$$
\text{Encrypted Data} = \text{PoseidonEnc}(\text{Content Chunks}, \text{Session Key})
$$

* The Seller encrypts the content chunks using the Session Key via the **Poseidon Cipher**.
* The resulting $\text{Encrypted Data}$ is pinned to IPFS, resulting in a **CID**.

### 3. ZK-SNARK Proof Generation

The Seller constructs a circuit to prove the following statements **without revealing the $\text{Content Chunks}$ or the $\text{Session Key}$**:

* **Statement 1 (Integrity):** "I know a set of $\text{Content Chunks}$ such that their hash equals $\text{Content Hash (Commitment)}$."
* **Statement 2 (Verifiable Encryption):** "I used the $\text{Session Key}$ correctly to generate the $\text{Encrypted Data}$ using $\text{PoseidonEnc}$, and this $\text{Encrypted Data}$ is publicly addressable via $\text{CID}$."

The output is the **ZK Proof ($\pi$)**, which is uploaded to the Solana listing contract. ****

### 4. On-Chain Verification

* The Solana Smart Contract runs the ZK Verifier function $V$.
* The Buyer (or the contract) confirms that: $V(\text{Public Inputs, ZK Proof } \pi) = \text{TRUE}$.

If verified, the entire transaction is deemed valid, and the exchange completes.

---

## 💻 Getting Started (Local Development)

### Prerequisites

* **Rust:** Stable version.
* **Solana Tool Suite:** Installed and configured.
* **Anchor Framework:** Installed for Smart Contract development.
* **ZK Tooling:** (e.g., circom/snarkjs, or a Rust ZK library integrated with Solana).

### Installation & Setup

```bash
# 1. Clone the repository
git clone [https://github.com/your-username/luminazk.git](https://github.com/your-username/luminazk.git)
cd luminazk

# 2. Build the Anchor Program
anchor build

# 3. Deploy the Smart Contract (to local validator or devnet)
solana-test-validator &
anchor deploy

# 4. Install Client Dependencies (Node.js)
cd client
npm install
