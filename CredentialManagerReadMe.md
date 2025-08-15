# CredentialManager - Comprehensive Credential Storage & Management

## 📋 Overview

CredentialManager is a centralized credential storage and management system within the Havoc C2 framework. It provides red teams with automated credential parsing, secure storage, and efficient organization of compromised credentials from multiple sources including hashdumps, SAM databases, LSA secrets, and NTDS.dit files.

## 🎯 Key Features

### **Comprehensive Credential Storage**
Complete credential management with support for multiple credential types:

- **Username/Password**: Clear-text password credentials
- **NTLM Hashes**: NT and LM hash storage
- **Domain Context**: Domain, workgroup, and local accounts
- **Host Information**: Source system identification
- **Credential Types**: Categorized credential classification
- **Metadata**: Notes, discovery time, and source information

### **Multi-Format Hash Parsing**
Automated parsing support for various hash dump formats:

#### **SAM Database Format**
```bash
# Standard SAM dump format
username:rid:lmhash:ntlmhash:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

#### **Simple Hash Format**
```bash
# Username:hash format
username:ntlmhash
admin:5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
user:31d6cfe0d16ae931b73c59d7e0c089c0
```

#### **Domain Format**
```bash
# Domain\username:hash format  
domain\username:ntlmhash
CORPORATE\admin:aad3b435b51404eeaad3b435b51404ee
CORPORATE\jdoe:5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
```

#### **NTDS.dit Format**
```bash
# Domain controller database format
domain\username:rid:lmhash:ntlmhash:::
CORPORATE\Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
CORPORATE\krbtgt:502:aad3b435b51404eeaad3b435b51404ee:d54c2ca3df9e9c4e5f0b9e8b7a1c5f7f:::
```

### **Intelligent Parsing Engine**
Advanced parsing capabilities with built-in intelligence:

- **Format Detection**: Automatic format recognition
- **Hash Validation**: NTLM hash format verification
- **Duplicate Filtering**: Automatic duplicate credential removal
- **Empty Hash Filtering**: Filters out default/empty hashes
- **Computer Account Exclusion**: Excludes machine accounts from NTDS parsing
- **RID Analysis**: Relative Identifier extraction and analysis

### **Advanced User Interface**
Feature-rich interface for credential management:

- **Sortable Tables**: Sort by any column (username, domain, type, etc.)
- **Context Menus**: Right-click operations for credentials
- **Clipboard Integration**: Easy credential copying
- **Search/Filter**: Real-time credential filtering
- **Bulk Operations**: Mass credential management
- **Visual Indicators**: Color-coded credential types

### **Export/Import Capabilities**
Flexible data portability options:

- **CSV Export**: Structured data export for analysis
- **JSON Format**: Machine-readable credential data
- **Import Functions**: Load credentials from external sources
- **Backup/Restore**: Operational continuity support

## 🚀 Usage Workflow

### **1. Automated Credential Collection**
```bash
# Execute credential harvesting commands
hashdump                    # Local SAM database
lsadump                     # LSA secrets
ntdsutil                    # Domain controller NTDS.dit
secretsdump.py              # Remote credential extraction

# CredentialManager automatically parses output from:
- hashdump BOF results
- SAM/LSA dump output  
- NTDS.dit extraction
- Manual hash input
```

### **2. Manual Credential Entry**
```bash
# Add credentials manually through UI
Username: administrator
Password: P@ssw0rd123
Domain: CORPORATE  
Host: DC01.corporate.local
Type: Password
Notes: Domain admin account from phishing
```

### **3. Bulk Hash Import**
```bash
# Paste hash dumps for automatic parsing
Raw Hash Data:
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
jdoe:1001:aad3b435b51404eeaad3b435b51404ee:5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8:::

# CredentialManager parses and stores automatically
```

### **4. Credential Usage & Export**
```bash
# Use stored credentials across Havoc tools
- JumpMan: Apply credentials for lateral movement
- LdapSearch: Use domain credentials for enumeration  
- ControlFreak: Execute commands with specific credentials

# Export for external tools
- Export to CSV for Bloodhound analysis
- JSON export for custom tools
- Copy individual credentials to clipboard
```

## 📊 Credential Storage Structure

### **Database Schema**
```cpp
struct CredentialItem {
    QString username;       // User account name
    QString password;       // Clear-text password (if available)
    QString ntlmHash;       // NTLM hash value
    QString domain;         // Domain or workgroup
    QString host;           // Source system
    QString type;           // Credential type (Password, Hash, etc.)
    QString notes;          // Additional information
    QDateTime discovered;   // Discovery timestamp
    QString source;         // Collection method
    bool validated;         // Credential validation status
};
```

### **Credential Table Display**
| Username | Domain | Type | Hash/Password | Host | Source | Notes |
|----------|--------|------|---------------|------|--------|-------|
| admin | CORP | Password | P@ssw0rd123 | WS001 | Manual | Domain admin |
| jdoe | CORP | NTLM | 5e884898... | DC01 | NTDS | Standard user |
| svc_sql | CORP | NTLM | aad3b435... | SQL01 | LSA | Service account |

## 🔍 Parsing Examples

### **SAM Database Parsing**
```bash
# Input: SAM dump output
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
jdoe:1001:aad3b435b51404eeaad3b435b51404ee:5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8:::

# Parsed Results:
✅ Administrator (RID: 500) - NTLM: 31d6cfe0... (Empty password)
❌ Guest (RID: 501) - Filtered (disabled account)  
❌ DefaultAccount (RID: 503) - Filtered (system account)
✅ jdoe (RID: 1001) - NTLM: 5e884898... (Valid hash)

# Intelligence Applied:
- Filters out empty passwords (31d6cfe0...)
- Excludes disabled/system accounts
- Extracts RID for analysis
- Validates hash format
```

### **NTDS.dit Parsing**
```bash
# Input: Domain controller NTDS dump
CORPORATE\Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
CORPORATE\Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
CORPORATE\krbtgt:502:aad3b435b51404eeaad3b435b51404ee:d54c2ca3df9e9c4e5f0b9e8b7a1c5f7f:::
CORPORATE\jdoe:1001:aad3b435b51404eeaad3b435b51404ee:5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8:::
CORPORATE\WORKSTATION01$:1005:aad3b435b51404eeaad3b435b51404ee:machine_account_hash:::

# Parsed Results:
✅ CORPORATE\Administrator - High-value target (RID 500)
❌ CORPORATE\Guest - Filtered (disabled)
✅ CORPORATE\krbtgt - Critical service account (RID 502)
✅ CORPORATE\jdoe - Standard domain user
❌ CORPORATE\WORKSTATION01$ - Filtered (computer account)

# Intelligence Applied:
- Extracts domain context
- Identifies high-value accounts (500, 502)
- Filters machine accounts ($)
- Preserves domain relationships
```

### **LSA Secrets Parsing**
```bash
# Input: LSA secrets dump
DPAPI_SYSTEM:01000000d08c9ddf0115d1118c7a00c04fc297eb...
$MACHINE.ACC:CORPORATE\WORKSTATION01$:aad3b435b51404eeaad3b435b51404ee:machine_hash
_SC_SQLServer:username:CORPORATE\svc_sql:password:SuperSecretP@ss!
_SC_MSSQL$SQL2019:username:CORPORATE\svc_mssql:password:AnotherP@ssw0rd

# Parsed Results:
❌ DPAPI_SYSTEM - Filtered (system secret)
❌ $MACHINE.ACC - Filtered (machine account)
✅ svc_sql - Service account with clear password
✅ svc_mssql - SQL service account with password

# Intelligence Applied:
- Extracts service account credentials
- Identifies clear-text passwords
- Filters system/machine secrets
- Preserves service context
```

## ⚙️ Advanced Features

### **Credential Validation**
```bash
# Automatic validation capabilities
NTLM Hash Validation:
- Format checking (32 character hex)
- Empty hash detection (31d6cfe0...)
- Hash type identification (NT vs LM)

Password Validation:  
- Clear-text password detection
- Common password identification
- Password policy compliance checking

Domain Context Validation:
- Domain format verification
- UPN vs NetBIOS format handling
- Cross-domain relationship tracking
```

### **Intelligent Filtering**
```cpp
// Built-in filtering logic
bool isValidCredential(const QString& username, const QString& hash) {
    // Filter computer accounts
    if (username.endsWith("$")) return false;
    
    // Filter empty hashes
    if (hash == "31d6cfe0d16ae931b73c59d7e0c089c0") return false;
    
    // Filter disabled accounts
    if (username.toLower().contains("guest")) return false;
    
    // Filter system accounts
    if (username.startsWith("$")) return false;
    
    return true;
}
```

### **Context Menu Operations**
```bash
# Right-click context menu options
Copy Username          # Copy username to clipboard
Copy Password/Hash     # Copy credential to clipboard  
Copy Full Credential   # Copy username:hash format
Edit Credential        # Modify credential details
Delete Credential      # Remove from database
Validate Credential    # Test credential validity
Mark as Compromised    # Flag as known compromise
Add Notes             # Append additional information
Export Single         # Export individual credential
```

### **Search & Filter Capabilities**
```bash
# Real-time search functionality
Search by Username: admin, administrator, service
Search by Domain: CORPORATE, WORKGROUP, LOCAL
Search by Type: Password, NTLM, Kerberos
Search by Host: DC01, SQL-SERVER, WORKSTATION
Search by Notes: phishing, lateral, persistence

# Advanced filtering
Username contains "admin"
Domain equals "CORPORATE"  
Type not equals "NTLM"
Host starts with "DC"
Discovered after "2024-01-01"
```

## 🛡️ Security Features

### **Data Protection**
```bash
# Secure credential handling
Encrypted Storage: Credentials encrypted at rest
Memory Protection: Secure memory allocation
Access Control: User-level access restrictions
Audit Logging: Credential access tracking

# Security Validations  
Input Sanitization: Prevent injection attacks
Format Validation: Ensure data integrity
Source Verification: Track credential origin
Update Tracking: Monitor credential changes
```

### **Privacy Controls**
```bash
# Sensitive data handling
Password Masking: Hide passwords in display
Clipboard Security: Secure clipboard operations
Export Controls: Restricted data export
Session Isolation: Separate credential contexts

# Compliance Features
Data Retention: Configurable retention policies
Access Logging: Comprehensive audit trails
Export Restrictions: Controlled data sharing
Secure Deletion: Cryptographic data wiping
```

## 🎯 Red Team Use Cases

### **Credential Harvesting Campaigns**
```bash
# Systematic credential collection
1. Execute hashdump across all compromised systems
2. Automatically parse and store all discovered hashes
3. Identify high-value accounts (RID 500, 502, adminCount=1)
4. Correlate credentials across multiple systems
5. Build comprehensive credential database

# Progressive Credential Discovery
Initial Access → Local Credentials → Domain Credentials → Enterprise Credentials
```

### **Lateral Movement Planning**
```bash
# Credential-based movement strategy
1. Analyze stored credentials for reuse patterns
2. Identify credentials with admin rights on multiple systems
3. Plan lateral movement paths using discovered credentials
4. Execute movement with JumpMan using stored credentials
5. Harvest additional credentials from new systems

# Cross-Domain Operations
1. Identify cross-domain trust credentials
2. Map credential usage across domain boundaries  
3. Plan multi-domain compromise strategies
4. Execute trust relationship exploitation
```

### **Privilege Escalation Operations**
```bash
# Administrative credential targeting
1. Filter credentials by high RID values (500-1000)
2. Identify service accounts with elevated privileges
3. Target accounts with adminCount=1 attribute
4. Focus on DA/EA/SA group memberships
5. Build privilege escalation pathways

# Service Account Exploitation
1. Extract service account credentials from LSA
2. Identify service accounts with interesting SPNs
3. Target high-privilege service contexts
4. Exploit service account delegation rights
```

### **Persistence & Backdoor Creation**
```bash
# Credential-based persistence
1. Create shadow admin accounts using discovered patterns
2. Modify service account passwords for persistence
3. Establish credential-based backdoors
4. Maintain access through credential rotation

# Long-term Access Maintenance
1. Monitor credential changes and updates
2. Track password policy compliance
3. Identify credential rotation patterns
4. Adapt persistence mechanisms accordingly
```

## 📈 Integration with Havoc Ecosystem

### **Tool Chain Integration**

#### **LdapSearch Integration**
```bash
# Workflow: LDAP → Credentials
1. Use LdapSearch to identify high-value accounts
2. Target identified accounts for credential extraction
3. Store extracted credentials in CredentialManager
4. Cross-reference LDAP data with credential database
```

#### **JumpMan Integration**
```bash
# Workflow: Credentials → Lateral Movement
1. Select credentials from CredentialManager
2. Apply credentials to JumpMan target list
3. Execute lateral movement with stored credentials
4. Store newly discovered credentials from movement
```

#### **ControlFreak Integration**
```bash
# Workflow: Multi-Beacon Credential Operations
1. Use ControlFreak to execute credential harvesting across beacons
2. Automatically parse results into CredentialManager
3. Apply stored credentials for subsequent ControlFreak operations
4. Coordinate credential-based operations across beacon set
```

### **Operational Workflow Integration**
```bash
# Complete Red Team Credential Lifecycle
Initial Access → Credential Harvesting → Storage → Analysis → Reuse → Expansion

1. Gain initial foothold on target network
2. Execute credential harvesting (hashdump, lsadump, etc.)
3. Automatically parse and store in CredentialManager
4. Analyze credentials for high-value targets
5. Apply credentials for lateral movement via JumpMan
6. Harvest additional credentials from new systems
7. Repeat cycle for full network compromise
```

## 📊 Export & Reporting

### **CSV Export Format**
```csv
Username,Domain,Type,Hash,Host,Source,Notes,Discovered
administrator,CORPORATE,NTLM,cf3a5525ee9414229e66279623ed5c58,DC01,NTDS,Domain admin,2024-01-15 14:30:00
jdoe,CORPORATE,Password,P@ssw0rd123,WS001,Manual,Standard user,2024-01-15 15:45:00
svc_sql,CORPORATE,NTLM,5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8,SQL01,LSA,Service account,2024-01-15 16:20:00
```

### **JSON Export Format**
```json
{
  "credentials": [
    {
      "username": "administrator",
      "domain": "CORPORATE",
      "type": "NTLM",
      "hash": "cf3a5525ee9414229e66279623ed5c58",
      "host": "DC01",
      "source": "NTDS",
      "notes": "Domain admin account",
      "discovered": "2024-01-15T14:30:00Z",
      "validated": true
    }
  ],
  "export_info": {
    "total_credentials": 247,
    "export_date": "2024-01-15T18:00:00Z",
    "havoc_version": "1.0.0"
  }
}
```

---

## 🔐 Best Practices

### **Operational Security**
- Regularly update and validate stored credentials
- Monitor for credential changes and policy updates
- Implement secure credential sharing procedures
- Maintain operational credential hygiene

### **Data Management**
- Organize credentials by campaign/engagement
- Implement proper data retention policies
- Secure credential backup and recovery procedures
- Regular credential database maintenance

### **Team Coordination**
- Establish credential sharing protocols
- Implement access control and audit procedures
- Coordinate credential usage across team members
- Maintain comprehensive operational documentation

---

*CredentialManager provides comprehensive credential storage, automated parsing, and intelligent management capabilities, making it essential for efficient credential lifecycle management during red team operations and ensuring maximum value extraction from compromised credential material.*