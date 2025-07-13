# X-Road Implementation for Government Services Integration

This project implements an X-Road infrastructure for secure data exchange between government agencies. The implementation includes a Central Server, Certification Authority, and multiple Security Servers for different government institutions.

## Project Structure

- **Central Server**: Core X-Road infrastructure component (running on port 4000)
  - Configuration management
  - Security server registration
  - Global configuration distribution

- **Certification Authority**: Provides PKI services (running on port 8888)
  - Certificate generation
  - OCSP services
  - Time-stamping services

- **Security Servers**:
  1. Management Security Server (SS1) - Ports: 1000, 1080, 1443
  2. Ministry of Finance (MINFIN) Security Server (SS2) - Ports: 2000, 2080, 2443
     - Provides Taxpayer Services
  3. Ministry of Justice (MINJUSDH) Security Server (SS3) - Ports: 3000, 3080, 3443

## Services Available

### Ministry of Finance (MINFIN)
- Taxpayer Information Services
- Service client management

### Ministry of Justice (MINJUSDH)
- Justice-related services
- Integrated service provision

## Technical Configuration

All security servers are configured with:
- Token PIN: 123456xrd!
- Admin User: xrd
- Admin Password: secret
- Log Level: INFO

## Container Architecture

The system uses Docker containers for all components:
- Base image for Security Servers: `niis/xroad-security-server-sidecar:7.6.1`
- Central Server image: `niis/xroad-central-server:noble-7.6.1`
- CA image: `ghcr.io/nordic-institute/xrddev-testca:latest`

All components are connected through the `xroad-network` bridge network.

## Data Persistence

The system uses named volumes for data persistence:
- Database volumes for each server
- Configuration volumes
- Archive volumes for security servers

## Backups

Regular backups are maintained for:
- Central Server configuration
- Security Server configurations
- System state and settings

## Getting Started

1. Clone this repository
2. Ensure Docker and Docker Compose are installed
3. Run the infrastructure:
   ```bash
   docker-compose up -d
   ```
4. Access the components:
   - Central Server: http://localhost:4000
   - MINFIN Security Server: http://localhost:2080
   - MINJUSDH Security Server: http://localhost:3080

## Security Notes

- All services run with secure communication enabled
- TLS endpoints are available on dedicated ports
- Default credentials should be changed in production

## Configured Subsystems and Services

### Management Security Server (SS1)
- **Subsystems**:
  - DPI (Department of Institutional Planning)
- **Services**:
  - DPI Information Service
- **Service Clients**: Configured to allow access from other government institutions

### Ministry of Finance (MINFIN) Security Server
- **Subsystems**:
  - SUNAT (National Superintendency of Tax Administration)
- **Services**:
  - Taxpayer Information Service
    - Provides real-time taxpayer data
    - Supports data exchange with other government entities
- **Service Clients**:
  - Configured access for authorized government departments
  - Integration with tax administration systems

### Ministry of Justice (MINJUSDH) Security Server
- **Subsystems**:
  - Legal Records Department
- **Services**:
  - Justice Information Service
    - Legal records access
    - Document verification services
- **Service Clients**:
  - Integrated access for authorized justice system participants
  - Cross-departmental data sharing capabilities

## X-Road Infrastructure Details

### Member Configuration
- All security servers are registered under the AOGOV X-Road instance
- Each institution is configured as a separate member
- Proper subsystem isolation ensures secure service provision

### Service Configuration
- Services are exposed through REST interfaces
- Each service is properly documented and versioned
- Access rights are managed through X-Road's security server configuration

## Actual Configuration Details (Based on Evidence)

### Central Server Configuration
Based on `X-road-members.png` and `X-road-security-servers.png`:
- Instance: AOGOV
- Registered Members:
  - GOV-MANAGEMENT
  - GOV-MINFIN
  - GOV-MINJUSDH

### Security Server Details

#### Management Security Server (SS1)
Evidence from `SS-DPI-Service.png`, `SS-DPI-ServiceClients.png`, `SS-Subsystems.png`:
- Owner: GOV-MANAGEMENT
- Subsystem: GOV-MANAGEMENT/DPI
- Service: getInformation
- Access Rights: Configured for both MINFIN and MINJUSDH subsystems

#### MINFIN Security Server
Evidence from `SSMINFIN-Subsystem.png`, `SSMINFIN-Taxpayer-Service.png`, `SSMINFIN-Taxpayer-ServiceClients.png`:
- Owner: GOV-MINFIN
- Subsystem: GOV-MINFIN/SUNAT
- Service: getTaxpayerInformation
- Access Rights: Granted to Management DPI subsystem

#### MINJUSDH Security Server
Evidence from `SSMINJUSDH-Service.png`, `SSMINJUSDH-ServiceClients.png`, `SSMINJUSDH-Subsystem.png`:
- Owner: GOV-MINJUSDH
- Subsystem: GOV-MINJUSDH/LEGAL
- Service: getLegalInformation
- Access Rights: Configured for interoperability with other registered subsystems

### Certificate Configuration
Located in `Certification Authority/Certs/`:
- Root CA Certificate (ca.pem)
- OCSP Certificate (ocsp.pem)
- Time-Stamping Certificate (tsa.pem)

### Backup Status
All servers have recent backups from May 21, 2025:
- Central Server: conf_backup_20250521-145550.gpg
- Management SS: conf_backup_20250521-145550.gpg
- MINFIN SS: conf_backup_20250521-150520.gpg
- MINJUSDH SS: conf_backup_20250521-150553.gpg
