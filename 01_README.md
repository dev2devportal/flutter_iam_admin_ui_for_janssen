# Flutter IAM Admin UI for Janssen

A comprehensive Flutter-based web administration interface for Janssen Identity and Access Management (IAM) server administration. This tool provides a modern, responsive interface for managing all aspects of Janssen IAM infrastructure including user management, role-based access control, multi-factor authentication, OAuth2/OpenID Connect configuration, SCIM provisioning, and integration with external service providers.

## License and Attribution
GNU Affero General Public License (AGPL)  
Author: Hawke Robinson  
Repository: https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen  
Created: 2024

## Overview
This administration interface provides comprehensive management capabilities for Janssen IAM servers, offering:

### Core IAM Features
- Complete user lifecycle management
- Role-based access control (RBAC)
- Multi-factor authentication (MFA)
- OAuth2/OpenID Connect management
- SAML integration
- SCIM provisioning
- UMA 2.0 support
- WebAuthn/FIDO2 configuration

### System Administration
- Server configuration management
- Performance monitoring
- Audit logging
- Security policy enforcement
- Certificate management

### Service Provider Integration
Built-in support for integrating with:
- Moodle LMS
- ERPNext
- Matrix.org servers
- Jitsi servers
- MediaVMS servers
- XWiki servers

## Quick Start
See [Installation Guide](docs/installation.md) for detailed setup instructions.

```bash
# Clone repository
git clone https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen.git
cd flutter_iam_admin_ui_for_janssen

# Install dependencies
flutter pub get

# Run development server
flutter run -d chrome --web-port=18080 --web-renderer=html
```

## Requirements
- Flutter SDK 3.22+
- Janssen Server 1.1.1+
- Web server (nginx/Apache)
- PostgreSQL 12+ or MySQL 8+
- Valid SSL certificates

## Documentation Index

### Setup & Configuration
- [Detailed Installation Guide](docs/installation.md)
- [Configuration Reference](docs/configuration.md)
- [Development Environment Setup](docs/development.md)
- [Production Deployment Guide](docs/deployment.md)

### Core Features
- [User Management](docs/features/user-management.md)
- [Role-Based Access Control](docs/features/rbac.md)
- [Multi-Factor Authentication](docs/features/mfa.md)
- [OAuth2/OpenID Connect](docs/features/oauth2-oidc.md)
- [SAML Configuration](docs/features/saml.md)
- [Audit Logging](docs/features/audit.md)

### Service Integration
- [Moodle Integration](docs/integrations/moodle.md)
- [ERPNext Integration](docs/integrations/erpnext.md)
- [Matrix.org Integration](docs/integrations/matrix.md)
- [Jitsi Integration](docs/integrations/jitsi.md)
- [MediaVMS Integration](docs/integrations/mediavms.md)
- [XWiki Integration](docs/integrations/xwiki.md)

### Development
- [Architecture Overview](docs/architecture.md)
- [Code Style Guide](docs/code-style.md)
- [Testing Guide](docs/testing.md)
- [Contributing Guidelines](docs/contributing.md)
- [API Documentation](docs/api.md)

### Examples & Tutorials
- [Basic Setup Tutorial](docs/tutorials/basic-setup.md)
- [User Management Example](docs/tutorials/user-management.md)
- [OAuth2 Configuration](docs/tutorials/oauth2-setup.md)
- [MFA Implementation](docs/tutorials/mfa-setup.md)

## Project Structure
```
lib/
├── config/           # Configuration management
├── core/            # Core utilities and constants
├── features/        # Feature modules
│   ├── auth/       # Authentication
│   ├── users/      # User management
│   ├── roles/      # Role management
│   ├── mfa/        # MFA configuration
│   └── oauth/      # OAuth2/OIDC management
├── services/        # Core services
├── shared/         # Shared components
└── main.dart       # Application entry
```

## Contributing
We welcome contributions! Please see our [Contributing Guidelines](docs/contributing.md) for details.

## Support
- [Issue Tracker](https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen/issues)
- [Discussion Forum](https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen/discussions)
- [Documentation Wiki](https://github.com/dev2devportal/flutter_iam_admin_ui_for_janssen/wiki)

## Roadmap
See our [Project Roadmap](docs/roadmap.md) for planned features and enhancements.

## Security
For security issues, please see our [Security Policy](SECURITY.md) and [Security Guide](docs/security.md).

## References
- [Janssen Documentation](https://docs.jans.io/)
- [Flutter Web Documentation](https://flutter.dev/web)
- [OAuth2 Specification](https://oauth.net/2/)
