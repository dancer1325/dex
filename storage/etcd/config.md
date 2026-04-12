# `type SSL struct {`
* == etcd databases's SSL options
* ``ServerName string `json:"serverName"``
  * == TLS hostname verification
  * allows
    * checking that the certificate == hostname / client is connecting to
* ``CAFile string `json:"caFile"``
  * == path -- to the -- CA file
* ``KeyFile string `json:"keyFile"``
  * == path -- to the -- private key file
* ``CertFile string `json:"certFile"``
  * == path -- to the -- private certificate file


# `type Etcd struct {`
* == Etcd options /
  * allows
    * connecting -- to -- etcd databases
* ``Endpoints []string `json:"endpoints"``
  * == etcd endpoints | Dex should connect to
* ``Namespace string `json:"namespace"``
  * == etcd namespace
    * ways to specify
      * this field
      * `--namespace=<prefix>`
          * == `etcd grpc-proxy --namespace=<prefix>`
  * uses
    * prefix ALL keys / created -- by -- etcd storage
    * set the connection
    * share your etcd cluster BETWEEN MULTIPLE applications
  * use cases
    * you are using a shared etcd cluster for storage
* ``Username string `json:"username"``
  * == username -- for -- etcd authentication
* ``Password string `json:"password"``
  * == password -- for -- etcd authentication
* ``SSL SSL `json:"ssl"``
