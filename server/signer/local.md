# `type LocalConfig struct {`
* == local signer's configuration
* ``KeysRotationPeriod string `json:"keysRotationPeriod"``
  * == duration / AFTER it,
    * signing keys will be rotated
* ``Algorithm jose.SignatureAlgorithm `json:"algorithm"``
  * == signing algorithm
    * uses
      * NEWLY generated local keys
    * if you change it -> applied
      * ❌NOT IMMEDIATELY❌
      * | rotation / Dex generates the NEXT signing key
  * supported values
    * RS256
    * ES256



# `type localSigner struct {`
* signs payloads -- via -- keys / stored | Dex storage
* manages key rotation & storage -- through -- the EXISTING keyRotator logic
* `storage storage.Storage`
* `rotator *keyRotator`
* `logger  *slog.Logger`
