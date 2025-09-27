# Hashkey_Desktop_app
Converts data to color hashes it and rebuilds it on seperate computer with no network needed


# Hashkey Desktop App

**Converts data to color hashes and rebuilds it on separate computers with no network needed**

## Overview

The Hashkey Desktop App is a secure, networkless file transfer system that uses cryptographic hashing and color-based data encoding. It allows you to generate hash keys from folded input data on one machine and verify/reconstruct that data on another machine without any network connection.

## System Architecture

The system consists of four core Python scripts that work together:

### 1. `color_fold_encoder.py` - Data Folding Engine
- **Purpose**: Converts RGB color values into expanded "folded" states
- **Function**: `expand_rgb_to_fold_state(r, g, b)`
- **Process**: 
  - Tri-vector expansion (multiplies RGB values by 3)
  - Converts to 8-bit binary and doubles the binary representation
  - Returns both folded RGB values and binary representation

### 2. `hashkey_generator.py` - Hash Key Creation
- **Purpose**: Generates cryptographic hash keys from user data and folded input
- **Key Features**:
  - Creates hash objects with UID, seed phrase, input data, and timestamp
  - Uses SHA-256 for cryptographic hashing
  - Saves hash metadata to `~/.ai_core/hashkeys/`
  - Creates `known_hash.txt` with the generated hash key

### 3. `hashkey_verifier.py` - Hash Validation
- **Purpose**: Verifies hash keys against saved metadata
- **Usage**: `python3 hashkey_verifier.py <folded_input.json> <known_hash.txt>`
- **Process**:
  - Loads metadata from user's saved hashkey file
  - Reconstructs original hash object
  - Compares generated hash with known hash

### 4. `q_memory_restorer.py` - Data Reconstruction
- **Purpose**: Recovers original folded input data from hash keys
- **Features**:
  - Automatic recovery from saved metadata files
  - Stateless recovery mode for manual reconstruction
  - Interactive input prompts for missing data

## Installation & Setup

### Prerequisites
- Python 3.6 or higher
- Required Python modules: `json`, `hashlib`, `os`, `pathlib`, `sys`, `time`

### Directory Structure
```
Hashkey_desktop_app/
├── color_fold_encoder.py
├── hashkey_generator.py
├── hashkey_verifier.py
├── q_memory_restorer.py
├── folded_input/
│   ├── folded_input.json
│   └── known_hash.txt
└── known_hash.txt
```

### Required Configuration Files

#### `folded_input/folded_input.json`
Create this file with your folded input data:
```json
{
  "r": 128,
  "g": 64,
  "b": 192,
  "frequency": 50,
  "hue": 0.75
}
```

**⚠️ CUSTOMIZE THESE VALUES:**
- `r`, `g`, `b`: RGB color values (0-255)
- `frequency`: Your custom frequency value
- `hue`: Hue value (0.0-1.0)

## Usage Workflow

### Step 1: Generate Hash Key (Source Machine)
```bash
python3 hashkey_generator.py
```
- Enter your unique User/Device UID
- Choose "G" for Generate new
- Enter your secret seed phrase
- System reads `folded_input/folded_input.json`
- Generates hash key and saves to `~/.ai_core/hashkeys/`

### Step 2: Transfer Hash Key (Networkless)
- Copy the generated `known_hash.txt` to target machine
- Optionally copy the metadata JSON file for easier verification

### Step 3: Verify Hash (Target Machine)
```bash
python3 hashkey_verifier.py folded_input/folded_input.json known_hash.txt
```
- Enter the same UID used during generation
- System verifies hash integrity

### Step 4: Reconstruct Data (If Needed)
```bash
python3 q_memory_restorer.py
```
- Enter UID and seed phrase
- System attempts to reconstruct original folded input data
- Supports both automatic and manual reconstruction modes

## Security Features

- **SHA-256 Cryptographic Hashing**: Military-grade hash function
- **Timestamp Integration**: Prevents replay attacks
- **UID-based Authentication**: User/device identification
- **Seed Phrase Security**: Additional entropy layer
- **Networkless Operation**: Air-gapped security

## Customization Requirements

### Author Information
Update the following in your scripts:
- Change `"== AI-Core HashKey Desktop Prototype =="` to your organization
- Update directory paths from `".ai_core"` to your preferred location

### Hash Algorithm
Currently uses SHA-256. To change:
```python
# In hashkey_generator.py and related files
return hashlib.sha256(json_bytes).hexdigest()  # Change to sha512, md5, etc.
```

### Storage Locations
Modify these paths in the scripts:
```python
# Default location
hashkeys_dir = Path.home() / ".ai_core" / "hashkeys"

# Customize to your needs
hashkeys_dir = Path.home() / ".your_app" / "keys"
```

## File Structure Explained

### Hash Object Structure
```json
{
  "uid": "user_device_identifier",
  "seed": "secret_seed_phrase", 
  "input": {
    "r": 128,
    "g": 64, 
    "b": 192,
    "frequency": 50,
    "hue": 0.75
  },
  "timestamp": 1672531200.0,
  "hash_key": "generated_sha256_hash"
}
```

### Known Hash File
Simple text file containing the generated hash:
```
a1b2c3d4e5f6789012345678901234567890abcdef1234567890abcdef123456
```

## Error Handling

The system includes robust error handling for:
- Missing files and directories
- Invalid JSON format
- Network connectivity issues (by design - networkless)
- Hash verification failures
- Invalid user input

## Security Best Practices

1. **Keep seed phrases secret** - Never share or store in plain text
2. **Use unique UIDs** - Different for each user/device combination  
3. **Secure folded_input.json** - This is your encryption key
4. **Verify hash integrity** - Always run verification on target machine
5. **Clean up temporary files** - Remove known_hash.txt after use

## Troubleshooting

### Common Issues:
- **"folded_input.json not found"**: Create the file with proper JSON structure
- **"Hash mismatch"**: Verify UID, seed phrase, and input data match exactly
- **"Metadata file not found"**: Run hash generation first, or use stateless recovery mode
- **Permission errors**: Ensure write access to home directory

## License

MIT License - See LICENSE file for details

## Contributing

This project is designed for secure, networkless file transfer. When contributing:
- Maintain cryptographic integrity
- Preserve networkless operation
- Follow secure coding practices
- Test thoroughly before deployment

---

**⚠️ SECURITY NOTICE**: This system is designed for air-gapped environments. Never transmit seed phrases or folded input data over unsecured networks.
