# `type VaultConfig struct {`
* == Vault signer's configuration
* ``Addr    string `json:"addr"``
  * == Vault/OpenBao server address
  * ALTERNATIVE
    * `VAULT_ADDR` environment variable
* ``Token   string `json:"token"``
  * == Vault/OpenBao authentication token
  * ALTERNATIVE
    * `VAULT_TOKEN` environment variable
* ``KeyName string `json:"keyName"``
  * MANDATORY
  * ALLOWED signing algorithms
    * `RS256`
      * == RSA / SHA-256)
    * `ES256`
      * == ECDSA / SHA-256
    * `ES384`
      * == ECDSA / SHA-384
    * `ES512`
      * == ECDSA / SHA-512
    * `EdDSA`
      * == Edwards-curve Digital Signature Algorithm



# `type vaultSigner struct {`
* signs payloads -- through -- HashiCorp Vault's Transit backend
* `client  *vault.Client`
* `keyName string`



# `type vaultAlgoParams struct {`
* `hasher      hash.Hash`
* `extraParams map[string]interface{}`
