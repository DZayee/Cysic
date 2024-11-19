# Cysic
This bash script is a setup script for configuring and running a verifier tool from **Cysic Labs**. Here's a detailed explanation of its components:

---

### **Script Overview**

#### **Purpose**:
- Automate the setup process for the Cysic verifier on macOS.
- Configure it to claim rewards to a specific Ethereum address (provided as an argument).

#### **Steps**:
1. **Input Validation**: Ensure the script is called with exactly one argument (`<claim_reward_address>`), which will be used in the configuration file.
2. **Setup Directories and Download Files**:
   - Remove any previous `cysic-verifier` directory.
   - Create a fresh `cysic-verifier` directory.
   - Download necessary files: `verifier_mac` (binary) and `libzkp.dylib` (dynamic library).
3. **Create Configuration File**:
   - Write a `config.yaml` file, including the claim reward address and chain-specific settings.
4. **Set Permissions and Start the Verifier**:
   - Make the `verifier_mac` binary executable.
   - Create a `start.sh` script for running the verifier and make it executable.

---

### **Code Analysis**

1. **Parameter Check**:
   ```bash
   if [ "$#" -ne 1 ]; then
       echo "Usage: $0 <claim_reward_address>"
       exit 1
   fi
   ```
   - Validates that the user provides exactly one argument, which is crucial for the script to proceed.

2. **File Setup**:
   ```bash
   rm -rf ~/cysic-verifier
   mkdir cysic-verifier
   curl -L https://github.com/cysic-labs/phase2_libs/releases/download/v1.0.0/verifier_mac > ~/cysic-verifier/verifier_mac
   curl -L https://github.com/cysic-labs/phase2_libs/releases/download/v1.0.0/libzkp.dylib > ~/cysic-verifier/libzkp.dylib
   ```
   - Ensures a clean slate by removing any existing verifier setup.
   - Downloads the verifier binary and library files from the official GitHub release.

3. **Configuration File**:
   ```bash
   cat <<EOF > ~/cysic-verifier/config.yaml
   # Not Change
   chain:
     # Not Change
     endpoint: "grpc-testnet.prover.xyz:80"
     chain_id: "cysicmint_9001-1"
     gas_coin: "CYS"
     gas_price: 10
     claim_reward_address: "$CLAIM_REWARD_ADDRESS"

   server:
     cysic_endpoint: "https://api-testnet.prover.xyz"
   EOF
   ```
   - Creates `config.yaml` with predefined values.
   - Inserts the user-provided `claim_reward_address`.

4. **Start Script**:
   ```bash
   echo "DYLD_LIBRARY_PATH=. CHAIN_ID=534352 ./verifier_mac" > ~/cysic-verifier/start.sh
   chmod +x ~/cysic-verifier/start.sh
   ```
   - Prepares an environment variable (`DYLD_LIBRARY_PATH`) to locate `libzkp.dylib`.
   - Sets the chain ID (534352) and runs the verifier binary.
   - Makes the start script executable for easy launch.

---

### **Usage**

1. **Run the Script**:
   ```bash
   ./setup_verifier.sh <claim_reward_address>
   ```
   Replace `<claim_reward_address>` with your actual Ethereum address.

2. **Start the Verifier**:
   ```bash
   cd ~/cysic-verifier/
   ./start.sh
   ```

---

### **Potential Enhancements**
- **Validation**:
  - Check the validity of the Ethereum address format (`claim_reward_address`).
  - Verify successful downloads of `verifier_mac` and `libzkp.dylib`.
- **Logging**:
  - Add logs for each step to help debug in case of errors.
- **Error Handling**:
  - Abort the script if `curl` or `chmod` commands fail.

Let me know if you'd like improvements or additions!
