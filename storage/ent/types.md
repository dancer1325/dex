# `type NetworkDB struct {`
* == SQL databases' COMMON options / accessed | network
* ``Database string``
* ``User string``
* ``Password string``
* ``Host string``
* ``Port uint16``
* ``ConnectionTimeout int``
  * seconds
* ``MaxOpenConns int``
  * default: `5`
* ``MaxIdleConns int``
  * default: `5`
* ``ConnMaxLifetime int``
  * seconds -- default: not set


# `type SSL struct {`
* == network databases' SSL options
* ``Mode string``
  * by default,
    * | Postgres,
      * "verify-full"
    * | MySQL,
      * "true"
* ``CAFile string``
* ``KeyFile string``
  * file -- for -- client auth
* ``CertFile string``
  * file -- for -- client auth
