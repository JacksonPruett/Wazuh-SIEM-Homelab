\# Wazuh Indexer



\## 1. Overview



The Wazuh Indexer is the storage and search component of the Wazuh Security Information and Event Management (SIEM) platform. It is based on OpenSearch and stores indexed security event data for searching, analysis, and visualization.



The Indexer works with the Wazuh Manager and Wazuh Dashboard to provide centralized security monitoring. The Manager processes security events and forwards applicable data to the Indexer, while the Dashboard provides a user interface for querying and analyzing that data.



\### Responsibilities



\- Store and index security events.

\- Support searches and queries against indexed data.

\- Provide the data backend for the Wazuh Dashboard.

\- Secure communications through TLS certificates.

\- Enforce authentication and authorization for protected API operations.



\## 2. Environment



| Property | Value |

|---|---|

| Hardware | Raspberry Pi 5, 8 GB RAM |

| Operating system | Debian GNU/Linux 13 (Trixie), on Raspberry Pi OS |

| Architecture | AArch64 (ARM64) |

| Wazuh Indexer version | 4.14.8-1 |

| Deployment model | Single-node deployment |

| Linux hostname | `soc-pi` |

| Indexer node name | `node-1` |

| Cluster name | `wazuh-cluster` |

| Configured network address | `192.168.12.137` |

| Indexer service | `wazuh-indexer` |

| HTTPS API port | `9200` |

| Transport port | `9300` |

| Configuration file | `/etc/wazuh-indexer/opensearch.yml` |

| Data directory | `/var/lib/wazuh-indexer` |

| Log directory | `/var/log/wazuh-indexer` |

| Certificate directory | `/etc/wazuh-indexer/certs/` |



The Indexer was installed on a Raspberry Pi 5 running a Debian 13-based operating system on ARM64 hardware.



During installation, Wazuh's installation tooling warned that the host operating system did not match its recommended platforms. The Indexer was subsequently installed and verified running on the system.



Because this deployment uses a platform that the installation tooling did not identify as a recommended target, compatibility should be reassessed before major upgrades.



\## 3. Installation and Service Configuration



The Wazuh Indexer was installed as a system package and registered with systemd.



\### 3.1 Installed Version



The installed package version was retrieved using:



```bash

dpkg-query -W -f='${Package} ${Version}\\n' wazuh-indexer

```



Observed output:



```text

wazuh-indexer 4.14.8-1

```



\### 3.2 Service Status



The service was inspected using:



```bash

sudo systemctl status wazuh-indexer --no-pager

```



Additional verification commands:



```bash

sudo systemctl is-active wazuh-indexer

sudo systemctl is-enabled wazuh-indexer

```



Observed results:



\- Service state: `active`

\- Startup configuration: `enabled`

\- Service activation time: September 29, 2026, at 13:18:52 EDT



The Indexer was confirmed to be running and enabled to start automatically during system startup.



\### 3.3 Service Management Commands



Check the service status:



```bash

sudo systemctl status wazuh-indexer --no-pager

```



Start the service:



```bash

sudo systemctl start wazuh-indexer

```



Stop the service:



```bash

sudo systemctl stop wazuh-indexer

```



Restart the service:



```bash

sudo systemctl restart wazuh-indexer

```



Check recent service logs:



```bash

sudo journalctl -u wazuh-indexer -n 50 --no-pager

```



These commands are executed in the Raspberry Pi's Linux shell, such as through an SSH session.



\## 4. Configuration



The primary Indexer configuration file is:



```text

/etc/wazuh-indexer/opensearch.yml

```



The following settings were observed in the configuration file:



```yaml

network.host: "192.168.12.137"

node.name: "node-1"

cluster.name: "wazuh-cluster"

path.data: /var/lib/wazuh-indexer

path.logs: /var/log/wazuh-indexer

```



\### 4.1 Network Configuration



The Indexer is configured to use `192.168.12.137` as its network address.



Two listening TCP ports were observed:



| Port | Purpose |

|---|---|

| `9200/TCP` | HTTPS REST API for Indexer requests |

| `9300/TCP` | Indexer transport communication |



The listening sockets were inspected with:



```bash

sudo ss -lntp | grep -E '(:9200|:9300)(\[\[:space:]]|$)'

```



Observed output:



```text

LISTEN 0 4096 \[::ffff:192.168.12.137]:9200 \*:\* users:(("java",pid=1761,fd=622))

LISTEN 0 4096 \[::ffff:192.168.12.137]:9300 \*:\* users:(("java",pid=1761,fd=620))

```



Both ports were listening on the configured address. The sockets were owned by the Java process running the Indexer.



The `\[::ffff:192.168.12.137]` notation represents an IPv4-mapped IPv6 address.



\### 4.2 Storage Configuration



The Indexer uses the following directories:



\- Data: `/var/lib/wazuh-indexer`

\- Logs: `/var/log/wazuh-indexer`



Disk usage was inspected with:



```bash

df -h /var/lib/wazuh-indexer /var/log/wazuh-indexer

```



At the time of inspection, both paths resided on the same filesystem.



| Filesystem metric | Observed value |

|---|---:|

| Total capacity | 235 GB |

| Used space | 26 GB |

| Available space | 200 GB |

| Utilization | 12% |



These values represent the entire filesystem, not the Indexer's individual data or log consumption.



\## 5. TLS Certificates and Security



The Indexer uses TLS for its HTTP API and transport communication.



The certificate directory is:



```text

/etc/wazuh-indexer/certs/

```



\### 5.1 Certificate Inventory



The following files were present during inspection:



| File | Purpose |

|---|---|

| `root-ca.pem` | Root certificate authority certificate |

| `indexer.pem` | Indexer node certificate |

| `indexer-key.pem` | Indexer node private key |

| `admin.pem` | Administrative client certificate |

| `admin-key.pem` | Administrative client private key |



\### 5.2 Ownership and Permissions



The inspected files were owned by the `wazuh-indexer` account and had permissions set to `400` (`-r--------`).



Example:



```text

\-r-------- 1 wazuh-indexer wazuh-indexer 1277 Sep 23 16:51 indexer.pem

\-r-------- 1 wazuh-indexer wazuh-indexer 1704 Sep 23 16:51 indexer-key.pem

\-r-------- 1 wazuh-indexer wazuh-indexer 1204 Sep 23 16:51 root-ca.pem

```



Permission mode `400` means that only the file owner has read permission. The group and other users have no direct file access.



Private keys must not be committed to the Git repository or published in project documentation.



\### 5.3 Indexer Certificate Metadata



Certificate metadata was inspected using:



```bash

sudo openssl x509 \\

&#x20; -in /etc/wazuh-indexer/certs/indexer.pem \\

&#x20; -noout -subject -issuer -dates -ext subjectAltName

```



Observed metadata:



```text

subject=C=US, L=California, O=Wazuh, OU=Wazuh, CN=node-1

issuer=OU=Wazuh, O=Wazuh, L=California

notBefore=Sep 23 20:51:05 2026 GMT

notAfter=Sep 20 20:51:05 2036 GMT

X509v3 Subject Alternative Name:

&#x20;   IP Address:192.168.12.137

```



The certificate's common name matches the configured Indexer node name, `node-1`. Its Subject Alternative Name contains the configured IP address, `192.168.12.137`.



The certificate is valid from September 23, 2026, through September 20, 2036, according to its recorded validity dates.



\### 5.4 Root Certificate Authority



The root CA certificate was inspected using:



```bash

sudo openssl x509 \\

&#x20; -in /etc/wazuh-indexer/certs/root-ca.pem \\

&#x20; -noout -subject -issuer -dates

```



Observed metadata:



```text

subject=OU=Wazuh, O=Wazuh, L=California

issuer=OU=Wazuh, O=Wazuh, L=California

notBefore=Sep 23 20:51:04 2026 GMT

notAfter=Sep 20 20:51:04 2036 GMT

```



The issuer and subject are identical, consistent with a self-signed root CA certificate.



The Indexer certificate and root CA have matching validity periods. Certificate metadata alone does not establish that the complete certificate chain is valid or that every connecting component trusts the same CA.



\### 5.5 TLS Configuration



The following TLS settings were present in `opensearch.yml`:



```yaml

plugins.security.ssl.http.pemcert\_filepath: /etc/wazuh-indexer/certs/indexer.pem

plugins.security.ssl.http.pemkey\_filepath: /etc/wazuh-indexer/certs/indexer-key.pem

plugins.security.ssl.http.pemtrustedcas\_filepath: /etc/wazuh-indexer/certs/root-ca.pem

plugins.security.ssl.transport.pemcert\_filepath: /etc/wazuh-indexer/certs/indexer.pem

plugins.security.ssl.transport.pemkey\_filepath: /etc/wazuh-indexer/certs/indexer-key.pem

plugins.security.ssl.transport.pemtrustedcas\_filepath: /etc/wazuh-indexer/certs/root-ca.pem

plugins.security.ssl.http.enabled: true

plugins.security.ssl.transport.enforce\_hostname\_verification: false

```



TLS is enabled for the HTTP API. The configuration references the Indexer certificate, its private key, and the root CA for both HTTP and transport communication.



\*\*Security note:\*\* Transport hostname verification is explicitly disabled in this configuration. This setting reduces hostname-identity checking for transport connections. Its necessity should be evaluated against the deployment's certificate identities and security requirements; it should not be changed without understanding the implications for the existing cluster configuration.



\## 6. Verification



Verification was performed at the service, network, and HTTP application layers.



\### 6.1 Service Verification



Command:



```bash

sudo systemctl is-active wazuh-indexer

```



Observed result:



```text

active

```



The service was running when inspected.



\### 6.2 Network Verification



Command:



```bash

sudo ss -lntp | grep -E '(:9200|:9300)(\[\[:space:]]|$)'

```



Both TCP ports `9200` and `9300` were listening on `192.168.12.137`.



This confirms the presence of listening sockets, but does not by itself establish successful communication with every dependent component.



\### 6.3 HTTPS API Verification



The HTTPS endpoint was tested using:



```bash

curl -k -o /dev/null -s -w 'HTTP status: %{http\_code}\\n' \\

&#x20; https://192.168.12.137:9200

```



Observed result:



```text

HTTP status: 401

```



The Indexer returned HTTP `401 Unauthorized`.



This demonstrates that the endpoint responded to the HTTPS request, but the request was not authorized. For a request without credentials, this is consistent with an endpoint that requires authentication.



The `-k` option disables certificate verification. Consequently, this test does not independently verify certificate trust or the certificate chain.



A complete end-to-end verification should also include a request using the appropriate trusted CA and valid credentials.



\## 7. Troubleshooting



\### 7.1 OpenSearch Security Not Initialized



\*\*Observed behavior\*\*



During the initial deployment, the Filebeat output test successfully completed URL parsing, DNS resolution, TCP connectivity, and TLS negotiation. The request then returned HTTP `503 Service Unavailable` with the message:



```text

OpenSearch Security not initialized

```



\*\*Analysis\*\*



The successful connection and TLS negotiation indicated that the client could reach the endpoint and establish a TLS session. The HTTP response indicated that the OpenSearch Security initialization process had not completed successfully at that time.



\*\*Troubleshooting approach\*\*



1\. Check the Indexer service status.

2\. Review the Indexer logs for initialization and certificate errors.

3\. Confirm that the intended certificates and configuration are installed.

4\. Complete the required security initialization procedure.

5\. Retest connectivity after initialization.



\### 7.2 Certificate Authority Mismatch



\*\*Observed behavior\*\*



During troubleshooting, a second certificate authority was generated. This resulted in a mismatch between the certificates and the trust configuration.



\*\*Impact\*\*



When components use certificates that do not chain to the CA trusted by the receiving service, secure connections may fail.



\*\*Troubleshooting approach\*\*



1\. Maintain a consistent certificate set for the deployment.

2\. Confirm that each component references the correct CA certificate.

3\. Verify certificate identities against configured node names and addresses.

4\. Restart affected services when required by configuration changes.

5\. Retest connectivity and authentication.



\### 7.3 HTTP 401 Unauthorized



\*\*Observed behavior\*\*



The Indexer's HTTPS endpoint returned HTTP `401 Unauthorized` during an unauthenticated request.



\*\*Analysis\*\*



The service responded, but the request was not authorized. This response is distinct from a connection failure or a TLS trust error.



\*\*Troubleshooting approach\*\*



1\. Confirm that the request targets the correct endpoint.

2\. Verify the authentication method and credentials.

3\. Confirm that the account has the required permissions.

4\. Retest using an authorized client and trusted CA.



\### 7.4 Java and OpenSearch Deprecation Warnings



\*\*Observed behavior\*\*



The service logs included warnings concerning deprecated Java security-manager functionality and locale-provider compatibility.



Examples included:



```text

System::setSecurityManager will be removed in a future release

```



\*\*Analysis\*\*



These messages were emitted during startup. The service subsequently reached the `Started wazuh-indexer.service` state and was later confirmed active.



The observed warnings did not prevent the service from starting. They should be monitored during future Wazuh, OpenSearch, and Java runtime upgrades.



\## 8. Operational Considerations



The following practices support the reliability and security of the Indexer:



\- Monitor the Indexer service and its logs.

\- Track available storage and filesystem utilization.

\- Keep certificate files and private keys restricted to authorized accounts.

\- Maintain consistent certificates and CA trust across communicating components.

\- Validate TLS trust independently of basic connectivity checks.

\- Review security-related configuration, including transport hostname verification.

\- Document compatibility warnings before upgrading the operating system or Wazuh components.

\- Back up necessary configuration and data before major maintenance.

\- Avoid publishing credentials, private keys, tokens, or other sensitive configuration in the Git repository.



\## 9. Verification Summary



| Check | Result |

|---|---|

| Installed package | `wazuh-indexer 4.14.8-1` |

| Service status | Active |

| Enabled at startup | Yes |

| Node name | `node-1` |

| Cluster name | `wazuh-cluster` |

| HTTP API listener | `192.168.12.137:9200` |

| Transport listener | `192.168.12.137:9300` |

| HTTP TLS enabled | Yes |

| Indexer certificate present | Yes |

| Root CA certificate present | Yes |

| Certificate IP SAN | Matches configured IP |

| Unauthenticated HTTPS response | HTTP `401 Unauthorized` |

| Filesystem utilization | 12% at time of inspection |



The Indexer service, its configured listeners, certificate files, and unauthenticated HTTPS endpoint were verified during this documentation session. Authenticated API access and full certificate-chain validation remain separate verification tasks.



\## 10. References



\- \[Wazuh Documentation](https://documentation.wazuh.com/)

\- \[Wazuh Indexer Installation Guide](https://documentation.wazuh.com/current/installation-guide/wazuh-indexer/index.html)

\- \[OpenSearch Documentation](https://docs.opensearch.org/)



