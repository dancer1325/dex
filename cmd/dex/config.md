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
  * == ALLOWED OAuth2 & OpenID Connect grant types
    * by default, ALL supported types


| Grant Type                                        | Description                                                                                                                                                                                                                                                                                                                                                                                       | Special Configuration                                                                |
|---------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| `authorization_code`                              | Authorization Code Flow <br/> uses <br/> &nbsp;&nbsp; web applications <br/> &nbsp;&nbsp; mobile applications                                                                                                                                                                                                                                                                                     | -                                                                                    |
| `refresh_token`                                   | allows <br/> &nbsp;&nbsp; clients can obtain NEW access tokens -- WITHOUT -- user interaction                                                                                                                                                                                                                                                                                                     | -                                                                                    |
| `password`                                        | Resource Owner Password Credentials Flow <br/> == clients send DIRECTLY -- , to the authorization server (Dex), -- user's credentials (`username` & `password`) <br/> &nbsp;&nbsp; acquiring access tokens  <br/> ⚠️deprecated⚠️ <br/> &nbsp;&nbsp; Reason:🧠[OAuth 2.0 Security Best Current Practice](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics-13#section-3.4) 🧠 | ⚠️requirements:⚠️ set `passwordConnector`                                            |
| `client_credentials`                              | Client Credentials Flow <br/> uses: server-to-server communication                                                                                                                                                                                                                                                                                                                                | ⚠️requirements:⚠️ feature flag `DEX_CLIENT_CREDENTIAL_GRANT_ENABLED_BY_DEFAULT=true` |
| `urn:ietf:params:oauth:grant-type:token-exchange` | Token Exchange Grant (RFC 8693)  <br/> allows <br/> &nbsp;&nbsp; clients can exchange tokens -- from -- EXTERNAL identity providers <br/> uses: OIDC connectors                                                                                                                                                                                                                                   | ⚠️requirements:⚠️ configure it ALSO \| `staticConnectors`                            |
| `urn:ietf:params:oauth:grant-type:device_code`    | Device Code Grant (RFC 8628) <br/> uses: devices / limited input capabilities                                                                                                                                                                                                                                                                                                                     | ⚠️requirements:⚠️ configure it ALSO \| `staticConnectors`                            |

* ``ResponseTypes []string `json:"responseTypes"``
  * allows you to -- , based on DIFFERENT values, -- configure the desired auth flow

| `responseTypes` value  | flow                    |
|------------------------|-------------------------|
| `code`                 | Authorization Code Flow |
| `id_token`             | Implicit Flow           |
| `id_token token`       | Implicit Flow           |
| `code id_token`        | Hybrid Flow             |
| `code token`           | Hybrid Flow             |
| `code id_token token`  | Hybrid Flow             |

* ``SkipApprovalScreen bool `json:"skipApprovalScreen"``
  * if
    * true -> logging == authorization
      * == ❌NO approval prompt❌
    * false -> users MUST approve data sharing / EACH auth flow
  * ❌if the request has the `approval_prompt=force` parameter -> this setting is NOT applicable❌
* ``AlwaysShowLoginScreen bool `json:"alwaysShowLoginScreen"``
  * if true -> display the login screen
  * if there is 1! enabled authentication method & default behavior (== `false`) -> go DIRECTLY -- to -- it
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
  * ALLOWED types
    * [local](../../server/signer/local.go)
    *
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
  * duration format: number + time unit (s, m, h)
    * _Example:_ 10m
* ``SigningKeys string `json:"signingKeys"``
  * == duration / AFTER it -> signingKeys will be rotated
    * recommendation
      * short
  * if `IDTokens` is exceeded -> signing keys' public parts are kept for validation TILL `SigningKeys` expiration
* ``IDTokens string `json:"idTokens"``
  * == duration | IdTokens will be valid
    * recommendation
      * short-lived
        * _Example:_ 10m
* ``AuthRequests string `json:"authRequests"``
  * == duration | AuthRequests (== exchange a code -- for an -- access OR id token) will be valid
* ``DeviceRequests string `json:"deviceRequests"``
  * == duration | DeviceRequests (== device can receive an access OR id token) will be valid
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
  * if you want to keep refresh tokens secure -> specify
    * `AbsoluteLifetime` OR
    * `ValidIfNotUsedFor`
  * pros
    * help deal with
      * network lags
      * concurrent requests
  * cons
    * tradeoff for security
* ``ReuseInterval string `json:"reuseInterval"``
  * == interval /
    * if the user's request contains the previous refresh token -> | refresh endpoint, get the SAME refresh token
  * pros
    * help deal with
      * network lags
      * concurrent requests
  * cons
    * tradeoff for security
* ``AbsoluteLifetime string `json:"absoluteLifetime"``
  * vs `ValidIfNotUsedFor`
    * stricter
  * force users to
    * reauthenticate
    * obtain a NEW refresh token
* ``ValidIfNotUsedFor string `json:"validIfNotUsedFor"``
  * if it is NOT used | specified amount of time -> invalidate a refresh token



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
