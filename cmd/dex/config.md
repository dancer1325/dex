# `type Config struct {`
* main application's config format
* ``Issuer    string    `json:"issuer"``
* ``Storage   Storage   `json:"storage"``
* ``Web       Web       `json:"web"``
* ``Telemetry Telemetry `json:"telemetry"``
* ``OAuth2    OAuth2    `json:"oauth2"``
* ``GRPC      GRPC      `json:"grpc"``
* ``Expiry    Expiry    `json:"expiry"``
* ``Logger    Logger    `json:"logger"``
* ``Frontend server.WebConfig `json:"frontend"``
* ``Signer Signer `json:"signer"``
  * controls
    * signing of JWT tokens / issued -- by -- Dex
* ``StaticConnectors []Connector `json:"connectors"``
  * == user defined connectors /
    * specified | ConfigMap
  * write operations (_Example:_ update a connector) will fail
* ``StaticClients []storage.Client `json:"staticClients"``
  * uses
    * by server
      * rather than querying the storage
  * Write operations (_Example:_ create a client) will fail
* ``EnablePasswordDB bool `json:"enablePasswordDB"``
  * if true -> server maintains a list of passwords -- to -- identify a user
* ``StaticPasswords []password `json:"staticPasswords"``
  * requirements
    * `enablePasswordDB`
  * uses
    * by server
      * rather than querying the storage
* ``Sessions *Sessions `json:"sessions"``
  * requirements
    * `DEX_SESSIONS_ENABLED=true` feature flag
  * == authentication session configuration
* ``MFA MFAConfig `json:"mfa"``
  * MULTI-factor authentication configuration


# `type MFAConfig struct {`
* == MULTI-factor authentication settings
* ``Authenticators []MFAAuthenticator `json:"authenticators"``
  * == MFA providers
  * uses
    * by clients
* ``DefaultMFAChain []string `json:"defaultMFAChain"``
  * `[]`
    * == NO MFA, by default
  * == default ordered list of authenticator IDs /
    * applied | clients / do NOT specify their OWN mfaChain


# `type password storage.Password`


# `type OAuth2 struct {`
* == enabled OAuth2 extensions
* ``GrantTypes []string `json:"grantTypes"``
  * == ALLOWED grant types
    * by default, ALL supported types
* ``ResponseTypes []string `json:"responseTypes"``
* ``SkipApprovalScreen bool `json:"skipApprovalScreen"``
  * if true -> logging == authorization
    * == ❌NO approval prompt❌
* ``AlwaysShowLoginScreen bool `json:"alwaysShowLoginScreen"``
  * if true -> show connector selection screen
    * ALTHOUGH there's 1!
* ``PasswordConnector string `json:"passwordConnector"``
  * == connector
    * uses
      * password grant
* ``PKCE PKCE `json:"pkce"``
  * == PKCE (Proof Key for Code Exchange) configuration


# `type PKCE struct {`
* == PKCE (Proof Key for Code Exchange) configuration
* ``Enforce bool `json:"enforce"``
  * if true -> PKCE is MANDATORY | ALL authorization code flows
* ``CodeChallengeMethodsSupported []string `json:"codeChallengeMethodsSupported"``
  * by default,
    * `["S256", "plain"]`


# `type Web struct {`
* == HTTP server's config format
* ``HTTP string `json:"http"``
* ``HTTPS string `json:"https"``
* ``Headers Headers `json:"headers"``
* ``TLSCert string `json:"tlsCert"``
* ``TLSKey string `json:"tlsKey"``
* ``TLSMinVersion string `json:"tlsMinVersion"``
* ``TLSMaxVersion string `json:"tlsMaxVersion"``
* ``AllowedOrigins []string `json:"allowedOrigins"``
* ``AllowedHeaders []string `json:"allowedHeaders"``
* ``ClientRemoteIP ClientRemoteIP `json:"clientRemoteIP"``


# `type ClientRemoteIP struct {`
* ``Header         string   `json:"header"``
* ``TrustedProxies []string `json:"trustedProxies"``


# `type Headers struct {`
* ``ContentSecurityPolicy string `json:"Content-Security-Policy"``
  * == HTTP responses' `Content-Security-Policy` header
  * if blank == unset
  * [docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)
* ``XFrameOptions string `json:"X-Frame-Options"``
  * == HTTP responses' `X-Frame-Options` header
  * if blank == unset
  * ALLOWED values
    * `deny`
    * `sameorigin`
  * [docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)
* ``XContentTypeOptions string `json:"X-Content-Type-Options"``
  * == HTTP responses' `X-Content-Type-Options` header
  * if blank == unset
  * ALLOWED values
    * `nosniff`
  * [docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options)
* ``XXSSProtection string `json:"X-XSS-Protection"``
  * == HTTP responses' `X-XSS-Protection` header
  * if blank == unset
  * [docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection)
* ``StrictTransportSecurity string `json:"Strict-Transport-Security"``
  * == HTTP responses' `Strict-Transport-Security` header
  * if blank == unset
  * [docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)


# `type Telemetry struct {`
* == telemetry's config format
  * ALSO include the HTTP server config
* ``HTTP string `json:"http"``
* ``EnableProfiling bool `json:"enableProfiling"``
  * enable the profiling endpoints -- via -- web interface
    * host:port/debug/pprof/


# `type GRPC struct {`
* == gRPC API's config
* ``Addr          string `json:"addr"``
  * == port | listen on
* ``TLSCert string `json:"tlsCert"``
* ``TLSKey string `json:"tlsKey"``
* ``TLSClientCA string `json:"tlsClientCA"``
* ``TLSMinVersion string `json:"tlsMinVersion"``
* ``TLSMaxVersion string `json:"tlsMaxVersion"``
* ``Reflection bool `json:"reflection"``


# `type Storage struct {`
* == app's storage configuration
* ``Type   string        `json:"type"``
* ``Config StorageConfig `json:"config"``


# `type StorageConfig interface {`
* == configuration /
  * can create a storage
* `Open(logger *slog.Logger) (storage.Storage, error)`




# `type Signer struct {`
* == app's signer configuration
* ``Type   string       `json:"type"``
* ``Config SignerConfig `json:"config"``



# `type SignerConfig interface{}`
* == configuration /
  * can create a signer


# `type Connector struct {`
* can
  * can unmarshal YAML DYNAMICALLY
* ``Type string `json:"type"``
  * == connector type /
    * customized -- for -- `Config`
* ``Name string `json:"name"``
* ``ID   string `json:"id"``
* ``Config     server.ConnectorConfig `json:"config"``
* ``GrantTypes []string               `json:"grantTypes"``



# `type Expiry struct {`
* == validity period of components configuration
* ``SigningKeys string `json:"signingKeys"``
  * == duration / AFTER it -> SigningKeys will be rotated
* ``IDTokens string `json:"idTokens"``
  * == duration | IdTokens will be valid
* ``AuthRequests string `json:"authRequests"``
  * == duration | AuthRequests will be valid
* ``DeviceRequests string `json:"deviceRequests"``
  * == duration | DeviceRequests will be valid
* ``RefreshTokens RefreshToken `json:"refreshTokens"``
  * == refresh tokens expiry policy


# `type Logger struct {`
* allows
  * customizing dex's loggin
* ``Level slog.Level `json:"level"``
  * logging level severity
* ``Format string `json:"format"``
  * logging format
* ``ExcludeFields []string `json:"excludeFields"``
  * log attribute keys / dropped | ALL log output
  * uses
    * suppress PII fields (_Example:_ `email`, `username`, `groups`) | GDPR environments


# `type RefreshToken struct {`
* ``DisableRotation bool `json:"disableRotation"``
* ``ReuseInterval string `json:"reuseInterval"``
* ``AbsoluteLifetime string `json:"absoluteLifetime"``
* ``ValidIfNotUsedFor string `json:"validIfNotUsedFor"``



# `type Sessions struct {`
* == authentication session configuration
* ``CookieName string `json:"cookieName"``
  * session cookie name
  * by default,
    * `"dex_session"`
* ``AbsoluteLifetime string `json:"absoluteLifetime"``
  * | creation, MAXIMUM session lifetime
  * by default,
    * `"24h"`
* ``ValidIfNotUsedFor string `json:"validIfNotUsedFor"``
  * idle timeout
  * by default,
    * `"1h"`
* ``RememberMeCheckedByDefault *bool `json:"rememberMeCheckedByDefault"``
  * "remember me" checkbox's default state
* ``CookieEncryptionKey string `json:"cookieEncryptionKey"``
  * == AES key -- for -- encrypting session cookies
    * ALLOWED bytes
      * 16
        * == AES-128
      * 24
        * == AES-192
      * 32 bytes
        * == AES-256
  * if "" -> cookies are NOT encrypted
* ``SSOSharedWithDefault string `json:"ssoSharedWithDefault"``
  * default SSO sharing policy -- for -- clients WITHOUT explicit `ssoSharedWith`
  * ALLOWED values
    * `"all"`
      * == share with ALL clients
    * `"none"`
      * == share with NO one
      * default


# `type MFAAuthenticator struct {`
* == MULTI-factor authentication provider
* ``ID string `json:"id"``
* ``Type string `json:"type"``
* ``Config json.RawMessage `json:"config"``
* ``ConnectorTypes []string `json:"connectorTypes"``
  * allows
    * limit the authenticator | SPECIFIC connector types
  * _Example:_ `ldap`, `oidc`, `saml`
  * if `[]` == applies | ALL connector types


# `type TOTPConfig struct {`
* == TOTP authenticator configuration
* ``Issuer string `json:"issuer"``
  * == service name /
    * shown | authenticator app



# `type WebAuthnConfig struct {`
* == WebAuthn authenticator configuration
* ``RPDisplayName string `json:"rpDisplayName"``
  * == human-readable relying party name / shown | browser dialog
    * _Example:_ `"My Company SSO"`
* ``RPID string `json:"rpID"``
  * == relying party identifier /
    * == domain | browser address bar
  * if "" -> derived -- from -- issuer URL hostname
    * _Example:_ `"auth.example.com"`
* ``RPOrigins []string `json:"rpOrigins"``
  * == ALLOWED WebAuthn ceremonies' origins
  * if "" -> derived -- from -- issuer URL
  * _Example:_ `["https://auth.example.com"]`
* ``AttestationPreference string `json:"attestationPreference"``
  * ALLOWED values
    * `"none"`
      * == NO request attestation
      * == simpler == MORE private
    * `"indirect"`
      * authenticator may anonymize attestation
      * default one
    * `"direct"`
      * request full attestation
      * uses
        * for enterprise key model verification
* ``UserVerification string `json:"userVerification"``
  * ALLOWED values
    * `"required"`
      * requirements
        * PIN
        * fingerprint
        * etc.
    * `"preferred"`
      * request if the authenticator supports it
      * default one
    * `"discouraged"`
      * skip verification
        * == ONLY check the presence
* ``AuthenticatorAttachment string `json:"authenticatorAttachment"``
  * ALLOWED values
    * `"platform"`
      * ONLY built-in
      * _Examples:_ Touch ID, Windows Hello
    * `"cross-platform"`
      * == ONLY external
      * _Examples:_ YubiKey, USB security keys
    * `""`
      * == ANY authenticator
      * default one
* ``Timeout string `json:"timeout"``
  * == ALLOWED browser WebAuthn ceremony's (registration or login) duration
  * by default,
    * `"60s"`
