# Guía de modificacion de prioridades 

[Volver a documento raíz](https://gitlab.unc.edu.ar/csirt/csirt-docs/tree/master#csirt-docs)

Se puede modificar las prioridades de las alertas de suricata que vienen por defecto. Las prioridades van desde 1 a 255 donde 1 es la prioridad mas alta y 255 la mas baja. Generalmente se utiliza un rango de prioridades de 1 a 4. 

Las reglas de suricata estan separadas por categoria. Las prioridades se pueden cambiar a una categoria o a una regla en particular, esto ultimo no es recomendable ya que las reglas se actualizan cada cierto tiempo y es tedioso cambiar cada una de las millones de reglas.

Las prioridades de una categoria se cambian desde el archivo **/etc/nsm/rules/classification.config**.
El archivo por defecto es:

```
config classification: attempted-admin,Attempted Administrator Privilege Gain,1
config classification: attempted-dos,Attempted Denial of Service,1
config classification: attempted-recon,Attempted Information Leak,2
config classification: attempted-user,Attempted User Privilege Gain,2
config classification: bad-unknown,Potentially Bad Traffic, 2
config classification: client-side-exploit,Known client side exploit attempt,1
config classification: coin-mining,Crypto Currency Mining Activity Detected,2
config classification: credential-theft,Successful Credential Theft Detected,2
config classification: default-login-attempt,Attempt to login by a default username and password,2
config classification: denial-of-service,Detection of a Denial of Service Attack,2
config classification: domain-c2,Domain Observed Used for C2 Detected,1
config classification: exploit-kit,Exploit Kit Activity Detected,1
config classification: external-ip-check,Device Retrieving External IP Address Detected,2
config classification: file-format,Known malicious file or file based exploit,2
config classification: icmp-event,Generic ICMP event,3
config classification: inappropriate-content,Inappropriate Content was Detected,2
config classification: kickass-porn,SCORE! Get the lotion!,2
config classification: malware-cnc,Known malware command and control traffic,1
config classification: misc-activity,Misc activity,3
config classification: misc-attack,Misc Attack,2
config classification: network-scan,Detection of a Network Scan,3
config classification: non-standard-protocol,Detection of a non-standard protocol or event,2
config classification: not-suspicious,Not Suspicious Traffic,3
config classification: policy-violation,Potential Corporate Privacy Violation,2
config classification: protocol-command-decode,Generic Protocol Command Decode,3
config classification: pup-activity,Possibly Unwanted Program Detected,2
config classification: rpc-portmap-decode,Decode of an RPC Query,2
config classification: sdf,Senstive Data,2
config classification: shellcode-detect,Executable code was detected,2
config classification: social-engineering,Possible Social Engineering Attempted,2
config classification: string-detect,A suspicious string was detected,3
config classification: successful-admin,Successful Administrator Privilege Gain,2
config classification: successful-dos,Denial of Service,1
config classification: successful-recon-largescale,Large Scale Information Leak,2
config classification: successful-recon-limited,Information Leak,2
config classification: successful-user,Successful User Privilege Gain,2
config classification: suspicious-filename-detect,A suspicious filename was detected,2
config classification: suspicious-login,An attempted login using a suspicious username was detected,2
config classification: system-call-detect,A system call was detected,2
config classification: targeted-activity,Targeted Malicious Activity was Detected,1
config classification: tcp-connection,A TCP connection was detected,4
config classification: trojan-activity,A Network Trojan was detected, 2
config classification: unknown,Unknown Traffic,3
config classification: unsuccessful-user,Unsuccessful User Privilege Gain,1
config classification: unusual-client-port-connection,A client was using an unusual port,2
config classification: web-application-activity,access to a potentially vulnerable web application,1
config classification: web-application-attack,Web Application Attack,1
```
## Fuentes
* https://suricata.readthedocs.io/en/suricata-4.1.4/rules/meta.html?highlight=classification
* http://manual-snort-org.s3-website-us-east-1.amazonaws.com/node31.html

