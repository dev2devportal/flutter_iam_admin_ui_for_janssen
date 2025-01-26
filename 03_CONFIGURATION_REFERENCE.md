# Configuration Reference

Comprehensive configuration guide for the Flutter IAM Admin UI for Janssen. This document details all available configuration options, their purposes, and example values.

## Table of Contents
- [Configuration Files Overview](#configuration-files-overview)
- [Janssen Server Configuration](#janssen-server-configuration)
- [OAuth2/OpenID Connect Configuration](#oauth2openid-connect-configuration)
- [UI Configuration](#ui-configuration)
- [Development Configuration](#development-configuration)
- [Production Configuration](#production-configuration)
- [Security Configuration](#security-configuration)
- [Integration Configuration](#integration-configuration)

## Configuration Files Overview

### File Structure
```
assets/
└── config/
    ├── janssen_config.yaml       # Janssen server settings
    ├── oauth2_config.yaml        # OAuth2/OIDC configuration
    ├── ui_config.yaml            # UI customization
    ├── development.yaml          # Development environment
    ├── production.yaml           # Production environment
    └── security_config.yaml      # Security settings
```

### Configuration Loading Order
1. Base configuration files
2. Environment-specific overrides
3. Runtime configuration
4. Environment variables

## Janssen Server Configuration

### Base Configuration (janssen_config.yaml)
```yaml
server:
  base:
    url: "https://your.janssen.server"
    version: "1.1.1"
    api_version: "v1"
    timeout: 30000  # milliseconds
    retry:
      attempts: 3
      backoff: 1000  # milliseconds
      max_backoff: 5000  # milliseconds

  endpoints:
    auth: "/jans-auth/restv1"
    config: "/jans-config-api/api/v1"
    scim: "/jans-scim/restv1"
    fido2: "/jans-fido2/restv1"
    token: "/jans-auth/restv1/token"
    userinfo: "/jans-auth/restv1/userinfo"
    
  caching:
    enabled: true
    type: "redis"  # redis, memory
    ttl: 3600  # seconds
    redis:
      host: "localhost"
      port: 6379
      db: 0
      password: null

  logging:
    level: "INFO"  # DEBUG, INFO, WARN, ERROR
    format: "json"
    file: "/var/log/janssen-admin.log"
    max_size: 10485760  # bytes
    max_files: 5
    syslog:
      enabled: false
      facility: "local0"

  monitoring:
    enabled: true
    metrics:
      port: 9090
      path: "/metrics"
    health:
      port: 8080
      path: "/health"
```

### Environment Variables
```bash
# Server configuration
JANSSEN_SERVER_URL=https://your.janssen.server
JANSSEN_API_VERSION=v1
JANSSEN_TIMEOUT=30000

# Logging
JANSSEN_LOG_LEVEL=INFO
JANSSEN_LOG_FORMAT=json

# Redis cache
JANSSEN_REDIS_HOST=localhost
JANSSEN_REDIS_PORT=6379
```

## OAuth2/OpenID Connect Configuration

### OAuth2 Settings (oauth2_config.yaml)
```yaml
oauth2:
  client:
    id: "your_client_id"
    secret: "your_client_secret"
    application_type: "web"
    subject_type: "pairwise"
    token_endpoint_auth_method: "client_secret_post"
    require_auth_time: true
    default_max_age: 86400
    
  authorization:
    response_types: 
      - "code"
      - "token"
      - "id_token"
    grant_types:
      - "authorization_code"
      - "implicit"
      - "refresh_token"
      - "client_credentials"
    
  scopes:
    default:
      - "openid"
      - "profile"
      - "email"
    admin:
      - "admin"
      - "manage_users"
      - "view_logs"
    custom:
      - "custom_scope1"
      - "custom_scope2"
    
  tokens:
    access_token:
      lifetime: 3600
      type: "JWT"
      signing_alg: "RS256"
    refresh_token:
      lifetime: 86400
      type: "JWT"
      reuse: false
    id_token:
      lifetime: 3600
      signing_alg: "RS256"
      encryption_alg: "RSA-OAEP"
      encryption_enc: "A128CBC-HS256"
    
  endpoints:
    authorization: "/oauth/authorize"
    token: "/oauth/token"
    userinfo: "/oauth/userinfo"
    jwks: "/oauth/jwks"
    registration: "/oauth/register"
    revocation: "/oauth/revoke"
    introspection: "/oauth/introspect"
    
  features:
    pkce:
      enabled: true
      required: false
    refresh_token:
      enabled: true
      rotation: true
    revocation:
      enabled: true
    introspection:
      enabled: true
```

## UI Configuration

### UI Settings (ui_config.yaml)
```yaml
ui:
  theme:
    mode: "system"  # light, dark, system
    colors:
      primary: "#1976D2"
      secondary: "#424242"
      success: "#4CAF50"
      error: "#F44336"
      warning: "#FF9800"
      info: "#2196F3"
    typography:
      font_family: "Roboto"
      scale_factor: 1.0
    
  layout:
    sidebar:
      width: 256
      expanded: true
      position: "left"  # left, right
    topbar:
      height: 64
      fixed: true
    content:
      max_width: 1200
      padding: 24
    
  features:
    dark_mode: true
    notifications: true
    animations: true
    search: true
    help_tooltips: true
    
  localization:
    default_locale: "en"
    supported_locales:
      - "en"
      - "es"
      - "fr"
    fallback_locale: "en"
    
  tables:
    default_page_size: 25
    available_page_sizes: [10, 25, 50, 100]
    enable_sorting: true
    enable_filtering: true
    
  forms:
    validation_mode: "onBlur"  # onChange, onSubmit
    show_required_marker: true
    enable_autosave: false
    
  notifications:
    position: "top-right"  # top-left, bottom-right, bottom-left
    duration: 5000  # milliseconds
    max_visible: 3
```

## Development Configuration

### Development Settings (development.yaml)
```yaml
development:
  server:
    use_proxy: true
    proxy_url: "http://localhost:8080"
    
  debugging:
    enable_logging: true
    verbose_errors: true
    show_network_info: true
    
  hot_reload:
    enabled: true
    port: 8000
    
  testing:
    mock_auth: false
    test_user:
      username: "test_admin"
      password: "test_password"
    
  performance:
    enable_profiling: true
    trace_widgets: true
    
  security:
    allow_insecure: true  # Development only!
    disable_csrf: false
```

## Production Configuration

### Production Settings (production.yaml)
```yaml
production:
  server:
    domain: "your.production.domain"
    use_cdn: true
    cdn_url: "https://cdn.your.domain"
    
  caching:
    enabled: true
    strategy: "aggressive"
    
  optimization:
    minify_js: true
    minify_css: true
    compress_images: true
    
  monitoring:
    error_reporting: true
    analytics: true
    performance_tracking: true
    
  backup:
    enabled: true
    frequency: "daily"
    retention_days: 30
    
  maintenance:
    enabled: false
    allowed_ips: []
    message: "System under maintenance"
```

## Security Configuration

### Security Settings (security_config.yaml)
```yaml
security:
  authentication:
    session_timeout: 3600
    max_attempts: 5
    lockout_duration: 900
    password_policy:
      min_length: 12
      require_uppercase: true
      require_lowercase: true
      require_numbers: true
      require_special: true
    
  authorization:
    default_role: "user"
    admin_roles:
      - "super_admin"
      - "system_admin"
    
  encryption:
    algorithm: "AES-256-GCM"
    key_size: 256
    key_rotation: 90  # days
    
  headers:
    hsts: true
    hsts_max_age: 31536000
    frame_options: "DENY"
    content_security_policy:
      default_src: ["'self'"]
      script_src: ["'self'", "'unsafe-inline'"]
      style_src: ["'self'", "'unsafe-inline'"]
    
  cors:
    allowed_origins:
      - "https://your.domain"
    allowed_methods:
      - "GET"
      - "POST"
      - "PUT"
      - "DELETE"
    allowed_headers:
      - "Content-Type"
      - "Authorization"
    expose_headers:
      - "Content-Length"
    max_age: 86400
```

## Integration Configuration

### Third-Party Integration Settings
```yaml
integrations:
  moodle:
    enabled: true
    base_url: "https://your.moodle.instance"
    client_id: "moodle_client_id"
    auth_endpoint: "/admin/oauth2callback.php"
    
  matrix:
    enabled: true
    homeserver_url: "https://matrix.your.domain"
    client_id: "matrix_client_id"
    
  jitsi:
    enabled: true
    server_url: "https://meet.your.domain"
    jwt_auth: true
    app_id: "jitsi_app_id"
    
  mediavms:
    enabled: true
    api_url: "https://media.your.domain/api"
    api_key: "your_api_key"
    
  xwiki:
    enabled: true
    base_url: "https://wiki.your.domain"
    auth_method: "oauth2"
```

### Environment-Specific Integration Overrides
```yaml
development:
  integrations:
    moodle:
      base_url: "http://localhost:8081"
    matrix:
      homeserver_url: "http://localhost:8008"

production:
  integrations:
    moodle:
      base_url: "https://lms.your.domain"
    matrix:
      homeserver_url: "https://matrix.your.domain"
```

## Configuration Best Practices

1. Security:
   - Never commit sensitive values
   - Use environment variables for secrets
   - Regularly rotate credentials
   - Enable security headers

2. Performance:
   - Enable caching in production
   - Configure appropriate timeouts
   - Set reasonable retry policies
   - Monitor resource usage

3. Maintenance:
   - Document all custom configurations
   - Keep backups of working configs
   - Test changes in development
   - Use version control

4. Integration:
   - Validate endpoints before deploying
   - Test authentication flows
   - Monitor integration health
   - Handle failures gracefully

## Additional Resources
- [Environment Setup Guide](environment.md)
- [Security Best Practices](security.md)
- [Performance Tuning](performance.md)
- [Integration Guide](integrations.md)
