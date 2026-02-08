# Project Specification: Bitcoin Wallet (Work In Progress)

## 1. Vision & Philosophy

To build the ultimate **Bitcoin-only** mobile application for iOS and Android. This project bridges the gap between daily spending and sovereign savings by combining **Local-first** data principles, **Self-custody**, and **Privacy**.

The goal is to eliminate friction between three financial layers:

1. **Spending** (Lightning/Cashu)
2. **Savings** (Onchain Hot Wallet)
3. **Cold Storage** (Hardware Wallet)

_The Design and UX will be inspired by the [Bitcoin Design Guide](https://bitcoin.design/guide/), but will feature a unique visual identity._

## 2. Technology Stack

- **Core:** React Native
- **Language:** TypeScript.
- **State & Sync:** **Evolu** (Local-first SQLite database + CRDTs).
  - Data lives on the device, encrypted.
  - P2P synchronization between user devices (no central surveillance server).
- **Cloud Backup (Disaster Recovery):**
  - **iOS:** iCloud Drive / CloudKit.
  - **Android:** Google Drive API (AppFolder).
  - _Note:_ All backups are encrypted client-side **before** upload. The cloud provider only sees an encrypted blob.
  - Keys for encryption are in secure enclave and can be backed up by social backup if user have trusted contacts. (Contacts can help user to recover the key, but they don't have access to the data.)
- **Bitcoin Stack:**
  - Onchain: `bdk-rn` (Bitcoin Dev Kit).
  - eCash: `cashu-ts` (or Rust port via FFI).
- **Hardware Connectivity:**
  - `react-native-ble-plx` (Bluetooth LE, iOS and Android).
  - `react-native-usb` (USB for Android).

## 3. Wallet Architecture (The 3 Vaults)

The app integrates three distinct asset management tiers into a single interface:

### Tier 1: Cashu Wallet (Hot / Spending)

- **Use Case:** Micro-payments, coffee, daily expenses, Lightning Network interaction.
- **Custody:** Custodial (vis-à-vis the Mint) but private (Blind Signatures).
- **Backup:** Tokens are automatically encrypted and backed up to the Cloud (iCloud/GDrive) upon every balance change.
- **UX:** Instant finality, near-zero fees.

### Tier 2: Onchain Hot Wallet (Warm / Savings)

- **Use Case:** Intermediate liquidity, larger amounts that need to be accessible without hardware.
- **Custody:** Self-custody (Seed stored in Secure Enclave/Keystore).
- **Backup:** Standard SLIP39 Seed backup + Optional Encrypted Cloud Backup (protected by a biometrics).

### Tier 3: Hardware Wallet (Cold / Vault)

- **Use Case:** Long-term savings (Life savings).
- **UX:** The app functions as a "Watch-only" interface. When sending, it prompts the user to connect the device (BT/USB) to sign.
- **Implementation:** Direct integration (No "Connect" web popups).

## 4. UI/UX Design

### Visual Identity

- **Style:** Minimalist, functional, industrial/cypherpunk aesthetic.
- **Palette:**
  - **Black (#0A0A0A) / Anthracite (#1F1F1F)**
  - **Gold (#F7931A)**
  - **Signal Yellow (#FDFD00)**
  - **White (#FFFFFF)**
  - **Grey (#808080)**
- **Typography:** Monospace for data/numbers (precision), Sans-serif for UI labels.

### Main Screen (Dashboard)

1. **Balance Header (Sticky):**
   - Dominant total balance in **Sats**.
   - Secondary balance in **Fiat** (USD/EUR/CZK).
   - Interactive: Tap/Swipe to filter view by Vault (Cashu vs. Onchain).

2. **Transaction Feed:**
   - Unified list (mixed Lightning, Cashu, and Onchain).
   - _Smart Metadata:_ Shows contact names/avatars (synced via Evolu).
   - _Status:_ Clear visual distinction for pending/unconfirmed transactions.

3. **Action Bar (Bottom Fixed):**
   - **iOS:** Two large, thumb-accessible buttons:
     - **[ ↓ RECEIVE ]**
     - **[ ↑ SEND ]**
   - **Android:** Material Design 3 Bottom Bar (or equivalent ergonomic layout).

## 5. Functional Specifications & Data Flow

### A. Connectivity & Hardware Flow

- **Bluetooth:**
  1. User initiates "Send from Cold Storage".
  2. App scans for BLE devices.
  3. Pairing occurs directly within the app UI (no settings switch needed).
  4. User confirms details on the hardware device.
  5. App receives the signature and broadcasts the transaction.
- **Network:**
  - Onchain data fetched via public or custom **Electrum Servers**.
  - Future support for Tor (Orbot/native integration) for privacy.

### B. Backup & Disaster Recovery

- **Evolu Data:**
  - Contacts, transaction metadata, and settings sync P2P between user devices.
  - Periodic snapshots are encrypted and sent to **iCloud/Google Drive**.
- **Recovery Scenario:**
  - User loses phone -> Installs app on new device -> Authenticates with Cloud -> App downloads and decrypts the database snapshot + Cashu tokens.

### C. Unified Payment Flow

1. **Scan:** User scans a QR code (Unified Scanner).
2. **Parse:** App detects type (Onchain Address, LN Invoice, LNURL, Cashu Token).
3. **Route & Select:**
   - _Small Amount / LN Invoice:_ Default to **Cashu Wallet**.
   - _Large Amount / Onchain Address:_ Offer **Hot Wallet** or **Hardware Wallet**.
   - _Auto-Swap:_ If the user wants to pay an LN Invoice but only has Onchain funds, the app performs a trustless submarine swap (via Boltz or similar) in the background.

## 6. Roadmap

### Phase 1: MVP

- Basic UI/UX implementation.
- Onchain Hot Wallet (BDK).
- Basic Cashu Wallet (Send/Receive).
- Local-first data (Evolu) setup.
- Unified Scanner.

### Phase 2: Hardware & Cloud

- Bluetooth & USB stack integration.
- Encrypted Cloud Backup implementation (iCloud/GDrive).

### Phase 3: Advanced Features

- Internal Swaps (Auto-balancing between Cashu and Cold Storage).
- Advanced Contact management.
