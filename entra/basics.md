# Entra ID Basics

Entra ID is Micro$oft's IDaaS (Identity as a Service) solution that offers a
cloud-native managed directory service. Entra ID provides features that aren't
available to a typical AD DS domain, such as MFA, PIM and self-induced password
resets.

Entra ID allows organizations to configure SSO for their enterprise, enable
federation between organizations and connect their on-prem identities to the
cloud.

Where AD DS has domains, Entra ID has tenants. While Entra ID serves a similar
service as AD DS, it is NOT the same as running AD DS on Azure compute resources.
Entra ID is focused on ID Management services for webapps while AD DS is intended
to be an on-prem solution.

## Tenants

Entra ID is multi-tenant by design. It is the "world's largest multi-tenant directory"
with each tenant representing an organization that has a subscription to a M$ cloud
based service (Entra ID is included with most of them). However, you can have multiple
tenants within a subscription. *An Azure subscription must be associated with one of*
*your tenants so that you can configure RBAC.* It is possible to associate multiple
Azure subscriptions with one tenant.

Tenants are named according to the convention `prefix.onmicrosoft.com` where the
prefix is the Microsoft account used to create the Azure subscription associated
with the tenant, or a custom one you put in at creation time.

*The tenant is the security boudary and container for Entra Objects like users,*
*groups and applications.*

### Cloud Apps as Tenants

When you deploy a cloud service like M365 or Intune you need a directory service
in the cloud alongside it to handle auth^2. Each cloud service that needs
authentication will make its own Entra tenant. Entra ID can also be used for SSO
to other applications.

## Schema

The Entra ID schema is similar to the AD DS schema but with less object types.
There is no computer class in the Entra schema but there is a device class.
Because device domain membership is different for Entra than AD DS, you can't
use GPOs to manage them and you can't use OUs to organize them. There are still
servicePrincipals and Applications, which are the instances of an application
within a tenant and the application definition respectively. This allows you
to define an application and then push servicePrincipals to multiple tenants.

## Differences Between AD DS and Entra ID

*AD DS* is an on-prem solution with the following major characteristics:

* AD DS is a true directory service, with a hierarchical X.500-based structure.
* AD DS uses Domain Name System (DNS) for locating resources such as domain controllers.
* You can query and manage AD DS by using Lightweight Directory Access Protocol (LDAP) calls.
* AD DS primarily uses the Kerberos protocol for authentication.
* AD DS uses OUs and GPOs for management.
* AD DS includes computer objects, representing computers that join an Active Directory domain.
* AD DS uses trusts between domains for delegated management.

You can deploy AD DS on Azure VMs to ensure an on-prem AD DS instance is scalable and
available, but that does not mean you are using Entra ID when you do this.

*Entra ID* is a cloud-native solution with the following major characteristics:

* Microsoft Entra ID is primarily an identity solution, and it’s designed for internet-based applications by using HTTP (port 80) and HTTPS (port 443) communications.
* Microsoft Entra ID is a multi-tenant directory service.
* Microsoft Entra users and groups are created in a flat structure, and there are no OUs or GPOs.
* You can't query Microsoft Entra ID by using LDAP; instead, Microsoft Entra ID uses the REST API over HTTP and HTTPS.
* Microsoft Entra ID doesn't use Kerberos authentication; instead, it uses HTTP and HTTPS protocols such as SAML, WS-Federation, and OpenID Connect for authentication, and uses OAuth for authorization. **You can use Entra Kerberos to authenticate to on-prem resources from your cloud identity**
* Microsoft Entra ID includes federation services, and many third-party services such as Facebook are federated with and trust Microsoft Entra ID.

## Entra Pricing Tiers (P1 and P2)

In addition to the Free and O365 editions of Entra you can also buy P1 or P2 tiers.
M$ charges per user provisioned. Tiered pricing includes a lot of stuff but the most
noable features are MFA, 99.9% uptime SLA and a bunch of dashboards for monitoring.
The P2 tier also includes Entra ID Protection for monitoring and protecting user
accounts and Entra PIM to configure extra security functionality for privileged
identities.

## Entra Domain Services

If you have an application running on a device that is a domain member, it will be
managed using AD DS. However, if you want to push it to the cloud you still need to
manage authentication but can't use AD DS. There used to be two ways to get around this:

1. Site-to-Site VPN between your on-prem and Azure resources.
2. Read-Only Domain Controllers from your on-prem AD DS as Azure VMs.

The problem is that with option 1 you have authentication traffic crossing the VPN
and with option 2 you have Domain Replication traffic crossing the VPN. Instead of
dealing with these security issues, you can buy P1 or P2 tier Entra and get access
to Group Policy management, domain joining and Entra Kerberos for the Entra tenant.
These services work with AD DS so you don't have to cloud replicate DCs.

Since Entra Domain lets you integrate with AD DS, you can then use Entra Connect
(which links AD DS and Entra Identities) to give users the ability to access
on-prem and Entra resources with their organizational credentials. If you don't have
AD DS running locally you can still use Entra DS as cloud-only and get similar
functionality as AD DS without a single domain controller anywhere.

*Interestingly enough, domains managed by Entra ID do not have to have Domain*
*Admins or Enterprise Admins*

Entra DS lets you migrate applications that use LDAP, NTLM or Kerberos from on-prem
to the cloud. *M$ Entra DS charges PER HOUR of uptime based on the size of the directory*
