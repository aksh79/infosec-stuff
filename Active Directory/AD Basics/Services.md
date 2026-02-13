- An identifier that indicates services available on a machine in a domain.
- No all services are registered in the domain.
- Registration for those services that need to authenticate users through kerberos
- Each service in AD has following data:
	- User that runs the service.
	- The service class indicating what kind of service is running.
	- The machine where the service is hosted.
	- Port on which the service is running - Optional
	- Path for the service

### SPN
Each service is identified using SPN - Service Principal Name
```bash
service_class/machine[:port][/path]
```
where the machine name can be the hostname or the FQDN.
```bash
ldap/WRK01 # hostname
ldap/WRK01.doctor.local # FQDN
```
- SPNs are stored in Computer/User object (service user) which actually runs the service 
https://en.hackndo.com/service-principal-name-spn/
