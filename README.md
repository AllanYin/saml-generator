SAML Response Generator
=======================

This is a small utility program that makes it easy to generate SAML responses for testing.

The output of this SAML response can be directly used for testing IDP Initiated SSO integration in AUTH0.

Creating Private and Public Keys for Testing
--------------------------------------------

You will need to generate a private and public key to use for generating saml assertions. The following steps are used for creating the keys:
```
#create the keypair
openssl req -new -x509 -days 3652 -nodes -out saml.crt -keyout saml.pem

#convert the private key to pkcs8 format
openssl pkcs8 -topk8 -inform PEM -outform DER -in saml.pem -out saml.pkcs8 -nocrypt
```

Command line tool
-----------------

You will need to create the jar file in order to use the command line tool. cd to saml-tutorial then run 'mvn package' to create a jar file called 'saml-generator-1.0.jar'. This jar file will be used to create saml assertions.

Usage
-----

java -jar saml-generator-1.0.jar [-domain <arg>] [-issuer <arg>] [-privateKey <arg>] [-publicKey <arg>] [-roles <arg>] [-email <arg>] [-samlAssertionExpirationDays <arg>] [-subject <arg>]

```
-issuer
The URI of the issuer for the saml assertion.

-subject
The username of the federated user.

-domain
The domain ID for the federated user.

-roles
A comma separated list of role names for the federated user.

-postBack
Post-back URL, also called Assertion Consumer Service URL. (Destination in SMAL response)

-entityId
Entity ID of the Service Provider. (Audience in SMAL conditons)

-email
The email address of the federated user.

-publicKey
THe path to the location of the public key to decrypt assertions

-privateKey
The path to the location of the private key to use to sign assertions

-samlAssertionExpirationDays
How long before the assertion is no longer valid
```

Example
-------

$ java -jar saml-generator-1.1.jar -domain 7719 -issuer 'http://XXX.com' -privateKey saml.pkcs8 -publicKey saml.crt -roles 'role1' -samlAssertionExpirationDays 5 -subject samlUser1 -entityId urn:auth0:XXX:YYY -postBack https://XXX.auth0.com/login/callback?YYY

Output:
```
<?xml version="1.0" encoding="UTF-8"?>
<saml2p:Response xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"
                 Destination="https://XXX.auth0.com/login/callback?YYY" ID="cba4d65c-4a69-4c24-a2b1-9b269daecfb0"
                 IssueInstant="2018-03-09T03:21:35.194Z" Version="2.0" xmlns:xs="http://www.w3.org/2001/XMLSchema">
    <saml2:Issuer xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">http://XXX.com</saml2:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>
            <ds:Reference URI="#cba4d65c-4a69-4c24-a2b1-9b269daecfb0">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
                        <ec:InclusiveNamespaces xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="xs"/>
                    </ds:Transform>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>
                <ds:DigestValue>eT2ul9N+Rf/f70wf0DEa/2ENPwk=</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>
            wsP+Cn9kbra8SDcBGnO2RI9zNd1sZWnhPlXiCkM8T2CUUkKBEPXJCHr/CtG89vGwDIMS8dtZ1sAbDndup7LaoB/OaYtHlx7vmsJ3IhLOeSieX0RG2mjnijv3TsUqaYAg7VqwICussUQDljuRAxXtL6VX5695BwPFzLCTTiQd9+cSz8NZwrFc+dCvvpkerpjjuTIgbc1aJkd/AdBLZ57xKkY04YA/YVny4+/q8YX2SxDCWkQS3wfN1QCx9f6huRkv+LAWd6hpBEZggxv4FffMN1jPEsicksdgBtH2cz2bExuhzsc1zDb9+AcD29sfHpQduVHEe8wh+ed7j6OyRH8vig==
        </ds:SignatureValue>
    </ds:Signature>
    <saml2p:Status>
        <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
    </saml2p:Status>
    <saml2:Assertion xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" ID="b5d9c1b9-73c3-47aa-880f-026ae9b27f8e"
                     IssueInstant="2018-03-09T03:21:35.056Z" Version="2.0">
        <saml2:Issuer>http://XXX.com</saml2:Issuer>
        <saml2:Subject>
            <saml2:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">samlUser1</saml2:NameID>
            <saml2:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
                <saml2:SubjectConfirmationData NotOnOrAfter="2018-03-14T03:21:35.046Z"/>
            </saml2:SubjectConfirmation>
        </saml2:Subject>
        <saml2:Conditions>
            <saml2:AudienceRestriction>
                <saml2:Audience>urn:auth0:XXX:YYY</saml2:Audience>
            </saml2:AudienceRestriction>
        </saml2:Conditions>
        <saml2:AuthnStatement AuthnInstant="2018-03-09T03:21:33.649Z">
            <saml2:AuthnContext>
                <saml2:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
                </saml2:AuthnContextClassRef>
            </saml2:AuthnContext>
        </saml2:AuthnStatement>
        <saml2:AttributeStatement>
            <saml2:Attribute Name="domain">
                <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">7719
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute Name="roles">
                <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xs:string">role1
                </saml2:AttributeValue>
            </saml2:Attribute>
        </saml2:AttributeStatement>
    </saml2:Assertion>
</saml2p:Response>

Base64Encoded:

PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz48c2FtbDJwOlJlc3BvbnNlIHht
bG5zOnNhbWwycD0idXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOnByb3RvY29sIiBEZXN0aW5h
dGlvbj0iaHR0cHM6Ly9YWFguYXV0aDAuY29tL2xvZ2luL2NhbGxiYWNrP1lZWSIgSUQ9ImNiYTRk
NjVjLTRhNjktNGMyNC1hMmIxLTliMjY5ZGFlY2ZiMCIgSXNzdWVJbnN0YW50PSIyMDE4LTAzLTA5
VDAzOjIxOjM1LjE5NFoiIFZlcnNpb249IjIuMCIgeG1sbnM6eHM9Imh0dHA6Ly93d3cudzMub3Jn
LzIwMDEvWE1MU2NoZW1hIj48c2FtbDI6SXNzdWVyIHhtbG5zOnNhbWwyPSJ1cm46b2FzaXM6bmFt
ZXM6dGM6U0FNTDoyLjA6YXNzZXJ0aW9uIj5odHRwOi8vWFhYLmNvbTwvc2FtbDI6SXNzdWVyPjxk
czpTaWduYXR1cmUgeG1sbnM6ZHM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvMDkveG1sZHNpZyMi
PjxkczpTaWduZWRJbmZvPjxkczpDYW5vbmljYWxpemF0aW9uTWV0aG9kIEFsZ29yaXRobT0iaHR0
cDovL3d3dy53My5vcmcvMjAwMS8xMC94bWwtZXhjLWMxNG4jIi8+PGRzOlNpZ25hdHVyZU1ldGhv
ZCBBbGdvcml0aG09Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvMDkveG1sZHNpZyNyc2Etc2hhMSIv
PjxkczpSZWZlcmVuY2UgVVJJPSIjY2JhNGQ2NWMtNGE2OS00YzI0LWEyYjEtOWIyNjlkYWVjZmIw
Ij48ZHM6VHJhbnNmb3Jtcz48ZHM6VHJhbnNmb3JtIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5v
cmcvMjAwMC8wOS94bWxkc2lnI2VudmVsb3BlZC1zaWduYXR1cmUiLz48ZHM6VHJhbnNmb3JtIEFs
Z29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvMjAwMS8xMC94bWwtZXhjLWMxNG4jIj48ZWM6SW5j
bHVzaXZlTmFtZXNwYWNlcyB4bWxuczplYz0iaHR0cDovL3d3dy53My5vcmcvMjAwMS8xMC94bWwt
ZXhjLWMxNG4jIiBQcmVmaXhMaXN0PSJ4cyIvPjwvZHM6VHJhbnNmb3JtPjwvZHM6VHJhbnNmb3Jt
cz48ZHM6RGlnZXN0TWV0aG9kIEFsZ29yaXRobT0iaHR0cDovL3d3dy53My5vcmcvMjAwMC8wOS94
bWxkc2lnI3NoYTEiLz48ZHM6RGlnZXN0VmFsdWU+ZVQydWw5TitSZi9mNzB3ZjBERWEvMkVOUHdr
PTwvZHM6RGlnZXN0VmFsdWU+PC9kczpSZWZlcmVuY2U+PC9kczpTaWduZWRJbmZvPjxkczpTaWdu
YXR1cmVWYWx1ZT53c1ArQ245a2JyYThTRGNCR25PMlJJOXpOZDFzWlduaFBsWGlDa004VDJDVVVr
S0JFUFhKQ0hyL0N0Rzg5dkd3RElNUzhkdFoxc0FiRG5kdXA3TGFvQi9PYVl0SGx4N3Ztc0ozSWhM
T2VTaWVYMFJHMm1qbmlqdjNUc1VxYVlBZzdWcXdJQ3Vzc1VRRGxqdVJBeFh0TDZWWDU2OTVCd1BG
ekxDVFRpUWQ5K2NTejhOWndyRmMrZEN2dnBrZXJwamp1VElnYmMxYUprZC9BZEJMWjU3eEtrWTA0
WUEvWVZueTQrL3E4WVgyU3hEQ1drUVMzd2ZOMVFDeDlmNmh1Umt2K0xBV2Q2aHBCRVpnZ3h2NEZm
Zk1OMWpQRXNpY2tzZGdCdEgyY3oyYkV4dWh6c2MxekRiOStBY0QyOXNmSHBRZHVWSEVlOHdoK2Vk
N2o2T3lSSDh2aWc9PTwvZHM6U2lnbmF0dXJlVmFsdWU+PC9kczpTaWduYXR1cmU+PHNhbWwycDpT
dGF0dXM+PHNhbWwycDpTdGF0dXNDb2RlIFZhbHVlPSJ1cm46b2FzaXM6bmFtZXM6dGM6U0FNTDoy
LjA6c3RhdHVzOlN1Y2Nlc3MiLz48L3NhbWwycDpTdGF0dXM+PHNhbWwyOkFzc2VydGlvbiB4bWxu
czpzYW1sMj0idXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOmFzc2VydGlvbiIgSUQ9ImI1ZDlj
MWI5LTczYzMtNDdhYS04ODBmLTAyNmFlOWIyN2Y4ZSIgSXNzdWVJbnN0YW50PSIyMDE4LTAzLTA5
VDAzOjIxOjM1LjA1NloiIFZlcnNpb249IjIuMCI+PHNhbWwyOklzc3Vlcj5odHRwOi8vWFhYLmNv
bTwvc2FtbDI6SXNzdWVyPjxzYW1sMjpTdWJqZWN0PjxzYW1sMjpOYW1lSUQgRm9ybWF0PSJ1cm46
b2FzaXM6bmFtZXM6dGM6U0FNTDoyLjA6bmFtZWlkLWZvcm1hdDpwZXJzaXN0ZW50Ij5zYW1sVXNl
cjE8L3NhbWwyOk5hbWVJRD48c2FtbDI6U3ViamVjdENvbmZpcm1hdGlvbiBNZXRob2Q9InVybjpv
YXNpczpuYW1lczp0YzpTQU1MOjIuMDpjbTpiZWFyZXIiPjxzYW1sMjpTdWJqZWN0Q29uZmlybWF0
aW9uRGF0YSBOb3RPbk9yQWZ0ZXI9IjIwMTgtMDMtMTRUMDM6MjE6MzUuMDQ2WiIvPjwvc2FtbDI6
U3ViamVjdENvbmZpcm1hdGlvbj48L3NhbWwyOlN1YmplY3Q+PHNhbWwyOkNvbmRpdGlvbnM+PHNh
bWwyOkF1ZGllbmNlUmVzdHJpY3Rpb24+PHNhbWwyOkF1ZGllbmNlPnVybjphdXRoMDpYWFg6WVlZ
PC9zYW1sMjpBdWRpZW5jZT48L3NhbWwyOkF1ZGllbmNlUmVzdHJpY3Rpb24+PC9zYW1sMjpDb25k
aXRpb25zPjxzYW1sMjpBdXRoblN0YXRlbWVudCBBdXRobkluc3RhbnQ9IjIwMTgtMDMtMDlUMDM6
MjE6MzMuNjQ5WiI+PHNhbWwyOkF1dGhuQ29udGV4dD48c2FtbDI6QXV0aG5Db250ZXh0Q2xhc3NS
ZWY+dXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOmFjOmNsYXNzZXM6UGFzc3dvcmRQcm90ZWN0
ZWRUcmFuc3BvcnQ8L3NhbWwyOkF1dGhuQ29udGV4dENsYXNzUmVmPjwvc2FtbDI6QXV0aG5Db250
ZXh0Pjwvc2FtbDI6QXV0aG5TdGF0ZW1lbnQ+PHNhbWwyOkF0dHJpYnV0ZVN0YXRlbWVudD48c2Ft
bDI6QXR0cmlidXRlIE5hbWU9ImRvbWFpbiI+PHNhbWwyOkF0dHJpYnV0ZVZhbHVlIHhtbG5zOnhz
aT0iaHR0cDovL3d3dy53My5vcmcvMjAwMS9YTUxTY2hlbWEtaW5zdGFuY2UiIHhzaTp0eXBlPSJ4
czpzdHJpbmciPjc3MTk8L3NhbWwyOkF0dHJpYnV0ZVZhbHVlPjwvc2FtbDI6QXR0cmlidXRlPjxz
YW1sMjpBdHRyaWJ1dGUgTmFtZT0icm9sZXMiPjxzYW1sMjpBdHRyaWJ1dGVWYWx1ZSB4bWxuczp4
c2k9Imh0dHA6Ly93d3cudzMub3JnLzIwMDEvWE1MU2NoZW1hLWluc3RhbmNlIiB4c2k6dHlwZT0i
eHM6c3RyaW5nIj5yb2xlMTwvc2FtbDI6QXR0cmlidXRlVmFsdWU+PC9zYW1sMjpBdHRyaWJ1dGU+
PC9zYW1sMjpBdHRyaWJ1dGVTdGF0ZW1lbnQ+PC9zYW1sMjpBc3NlcnRpb24+PC9zYW1sMnA6UmVz
cG9uc2U+

```
