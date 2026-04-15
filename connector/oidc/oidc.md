# `type Config struct {`
* == OpenID Connect logins' configuration options
* ``Issuer string `json:"issuer"``
  * == provider's canonical URL
  * == discovery GET ".well-known/openid-configuration"'s response `issuer`
    * [documentation](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)
* ``IssuerAlias  string `json:"issuerAlias"``
  * uses
    * providers / have oidc discovery url != issuer url
      * -> fail issuerValidation
      * _Examples:_ Azure, Oracle IDCS
  * allows
    * override the Issuer url -- from the -- ".well-known/openid-configuration" issuer
* ``ClientID     string `json:"clientID"``
* ``ClientSecret string `json:"clientSecret"``
* ``RedirectURI  string `json:"redirectURI"``
* ``ProviderDiscoveryOverrides ProviderDiscoveryOverrides `json:"providerDiscoveryOverrides"``
  * override options / discovered AUTOMATICALLY -- from -- the providers' discovery URL (".well-known/openid-configuration")
* ``BasicAuthUnsupported *bool `json:"basicAuthUnsupported"``
  * `client_secret` are passed -- as -- POST parameters
    * ❌!= -- as -- basic auth❌
  * ❌NOT recommended❌
    * -- by the -- OAuth2 RFC
  * requirements
    * [-- by -- SOME providers](https://tools.ietf.org/html/rfc6749#section-2.3.1)
* ``Scopes []string `json:"scopes"`
  * == ADDITIONAL scopes / request | token response
  * by default, `["profile", "email"]`
* ``HostedDomains []string `json:"hostedDomains"``
  * ⚠️deprecated⚠️
    * use Google connector
  * OPTIONAL
  * == whitelisted domains
    * == ONLY users | whitelisted domain,
      *  can log in
  * use cases
    * use the OIDC connector + Google
* ``RootCAs []string `json:"rootCAs"``
  * == SSL validation certificates
* ``InsecureSkipEmailVerified bool `json:"insecureSkipEmailVerified"``
  * | returned claims,
    * override the value of `email_verified` / set to `true`
  * use cases
    * claims / WITHOUT `email_verified`
      * _Examples:_ providers /
        * | enrollment process, NO use emails verifications
        * act -- as a proxy for -- ANOTHER IDP
          * _Example:_ App → OIDC → Dex → OIDC → AWS Cognito → SAML → Enterprise SAML IdP
* ``InsecureEnableGroups bool     `json:"insecureEnableGroups"``
  * enables
    * groups claims
      * == trust that upstream provider provides valid groups
  * TILL [this issue](https://github.com/dexidp/dex/issues/1065) is resolved,
    * by default, disabled
      * == ❌NOT ALLOW group claims❌
  * use cases
    * ONLY | refresh the id token, refresh groups claims
      * == ❌regular refresh flow does NOT update the groups claim❌
* ``AllowedGroups        []string `json:"allowedGroups"``
  * allows
    * override `InsecureEnableGroups`
  * filter users -- based on -- membership | given groups
    * if user is included | >=1 allowedGroup -> successfully authentication
* ``AcrValues []string `json:"acrValues"``
  * == Authentication Context Class Reference Values
  * allows
    * specifying the Authentication Request's Authentication Context Class Values /
      * -- to -- Authorization Server
      * appear -- through -- preference order

* ``InsecureSkipVerify bool `json:"insecureSkipVerify"``
  * == disable certificate verification

* ``GetUserInfo bool `json:"getUserInfo"``
  * get token's ADDITIONAL claims -- through -- `userinfo_endpoint`
    * userInfo's claims' priority > IDToken's claims' priority
  * use cases
    * upstreams return "thin" id tokens
      * [MORE](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo)

* ``UserIDKey string `json:"userIDKey"``
  * by default,
    * `sub`
  * [MORE](https://openid.net/specs/openid-connect-core-1_0.html#Claims)

* ``UserNameKey string `json:"userNameKey"``
  * by default,
    * `name`

* ``PromptType *string `json:"promptType"``
  * allows
    * override the OAuth2's `prompt` parameter
      * if request `scope=offline_access` -> by default `prompt=consent`
  * ❌NOT supported | ALL OIDC providers❌
  * _Example of ALLOWED values:_
    * `login`
    * `none`

* ``PKCEChallenge string `json:"pkceChallenge"``
  * == PKCE algorithm / will be used
    * if you do NOT specify it -> auto-detect the BEST-fit for the connector

* ``OverrideClaimMapping bool `json:"overrideClaimMapping"``
  * by default,
    * `false`
  * allows
    * enforce `ClaimMapping`'s options
      * != use default options

* `ClaimMapping struct {           json:"claimMapping"`
* `ClaimMutations struct {         json:"claimModifications"`

## `ClaimMapping struct {`
* allows
  * mapping NON-standard claims -- to -- [standard claims](https://openid.net/specs/openid-connect-core-1_0.html#Claims)
    * if the NON-standard claims is NOT returned | `id_token` -> can ONLY map 1! NON-standard claim
* uses
  * providers / return NON-standard claims
    * _Example:_ `mail` -- rather than -- `email`
* ``PreferredUsernameKey string `json:"preferred_username"``
  * == set claim -- is used as -- preferred username
  * == configurable key /
    * contains the PREFERRED username claims
  * by default,
    * "preferred_username"
* ``EmailKey string `json:"email"``
  * == set claim -- is used as -- email
  * == configurable key /
    * contains the email claims
  * by default,
    * "email"
* ``GroupsKey string `json:"groups"``
  * == set claim -- is used as -- groups
  * == configurable key /
    * contains the groups claims
  * by default,
    * "groups"

## `ClaimMutations struct {`
* == claim mutations options
* allows
  * | login, change claims
* ``NewGroupFromClaims []NewGroupFromClaims `json:"newGroupFromClaims"``
  * allows
    * creating a NEW group -- based on -- OTHER claims /
      * concatenated -- through -- the delimiter
* ``FilterGroupClaims  FilterGroupClaims    `json:"filterGroupClaims"``
* ``ModifyGroupNames   ModifyGroupNames     `json:"modifyGroupNames"``


# `type ProviderDiscoveryOverrides struct {`
* ``TokenURL string `json:"tokenURL"``
  * allows
    * override the token_endpoint value / returned | call ".well-known/openid-configuration"

* ``AuthURL string `json:"authURL"``
  * allows
    * override the authorization_endpoint value / returned | call ".well-known/openid-configuration"

* ``JWKSURL string `json:"jwksURL"``
  * allows
      * override the jwks_uri value / returned | call ".well-known/openid-configuration"

* ``EndSessionURL string `json:"endSessionURL"``
  * allows
      * override the end_session_endpoint value / returned | call ".well-known/openid-configuration"



# `type NewGroupFromClaims struct {`
* allows
  * FROM a list of claims, creates a NEW group /
    * appends it | list of EXISTING groups
* ``Claims []string `json:"claims"``
  * == claimS / join together
  * if you do NOT provide string -> skipped

* ``Delimiter string `json:"delimiter"``
  * allows
    * separate the claims

* ``ClearDelimiter bool `json:"clearDelimiter"``
  * allows
    * removing the Delimiter string | claim values
  * uses
    * keep resulting claim structure -- in full control of the -- Dex operator

* ``Prefix string `json:"prefix"``
  * placed BEFORE the FIRST claim


# `type FilterGroupClaims struct {`
* == regex filter /
  * ONLY keep the matching groups
  * follow [RE2](https://github.com/google/re2)
  * ❌NOT apply | groups / added -- through -- `newGroupFromClaims`❌
* allows
  * fit | HTTP header
* uses
  * groups list is too large
* ``GroupsFilter string `json:"groupsFilter"``


# `type ModifyGroupNames struct {`
* allows
  * modify the group claims / EACH group
    * ❌NOT apply | groups /
      * NOT filtered -- by -- filterGroupClaims
      * BEFORE creating newGroupFromClaims❌
* ``Prefix string `json:"prefix"``
* ``Suffix string `json:"suffix"``



# `type connectorData struct {`
* uses
  * store information -- for -- sessions / authenticated by this connector
* `RefreshToken []byte`

# `type PKCEChallengeData struct {`
* uses
  * | connectorData, store info -- for --
    * PKCE Challenge method
    * verifier
* ``CodeChallenge       string `json:"codeChallenge"``
* ``CodeChallengeMethod string `json:"codeChallengeMethod"``

# `type oidcConnector struct {`
* `provider                  *oidc.Provider`
* `redirectURI string`
* `oauth2Config *oauth2.Config`
* `verifier *oidc.IDTokenVerifier`
* `cancel context.CancelFunc`
* `logger *slog.Logger`
* `httpClient *http.Client`
* `insecureSkipEmailVerified bool`
* `insecureEnableGroups bool`
* `allowedGroups []string`
* `acrValues []string`
* `getUserInfo bool`
* `promptType string`
* `userIDKey string`
* `userNameKey string`
* `overrideClaimMapping bool`
* `preferredUsernameKey string`
* `emailKey string`
* `groupsKey string`
* `newGroupFromClaims []NewGroupFromClaims`
* `groupsFilter *regexp.Regexp`
* `groupsPrefix string`
* `groupsSuffix string`
* `pkceChallenge string`
* `endSessionURL string`

# `type oauth2Error struct {`
* `error            string`
* `errorDescription string`


# `type caller uint`
