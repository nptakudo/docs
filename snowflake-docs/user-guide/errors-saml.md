---
title: "Federated authentication and SSO troubleshooting"
url: "https://docs.snowflake.com/en/user-guide/errors-saml"
---

# Federated authentication and SSO troubleshooting

This topic provides information to help troubleshoot a federated authentication environment, including the error codes and messages that
are generated during an unsuccessful user login attempt.

## Password-related errors

A user with an expired Snowflake password cannot log in with SSO even though they are not using the password. This behavior is
intentional and prevents someone from logging in using expired credentials.

SSO logins are also rejected if an administrator set the `MUST_CHANGE_PASSWORD` parameter to TRUE when creating the user, but the user
has not changed the password yet.

## Error codes

Errors are generated for each failed login attempt. These errors can be obtained from the [Snowflake Information Schema](../sql-reference/info-schema) or the
[ACCOUNT\_USAGE schema](../sql-reference/account-usage):

* The Snowflake Information Schema provides data from within the past 7 days and can be queried using
  the [LOGIN\_HISTORY , LOGIN\_HISTORY\_BY\_USER](../sql-reference/functions/login_history) table functions.
* The [LOGIN\_HISTORY](../sql-reference/account-usage/login_history) view in the ACCOUNT\_USAGE schema provides similar data from within the past year.

### Federated authentication error codes

The table below contains the error codes and messages related to federated authentication.

| Error Code | Error | Description |
| --- | --- | --- |
| 390136 | FED\_REAUTH\_PENDING | Authentication response is pending from IDP. |
| 390137 | FED\_REAUTH | Federated authentication request URL is generated. |
| 390138 | FED\_REAUTH\_TIMEOUT | Timeout waiting for authentication response from IDP. |
| 390139 | AUTHENTICATOR\_NOT\_SUPPORTED | The specified authenticator is not accepted by your Snowflake account configuration. Please contact your local system administrator to get the correct URL to use. |
| 390140 | FED\_PASSWORD\_EXPIRED | Identity Provider (IdP) password has expired. Contact your IdP team. |
| 390191 | USERNAMES\_MISMATCH | The user you were trying to authenticate as differs from the user currently logged in at the IDP. |

See moreShow less

Expand

### SAML error codes

Troubleshooting a login failure differs depending on whether the error message has an UUID.

If you encounter an error message associated with a failed SAML SSO login attempt, and the error message does not have a UUID, then ensure
the user exists. If the user exists, then the SAML response is invalid and the number of login attempts is too high.

If you encounter an error message associated with a failed SAML SSO login attempt, and the error message has a UUID, you can ask an
administrator that has MONITOR privilege assigned to their role to get a more detailed description of the error by following the steps
below:

1. Find the UUID in the error message:

   > Expand
   >
   > ```
   > SAML response is invalid or matching user is not found. Contact your local system administrator. [eb55b777-50a4-4db5-b231-9ee457fb3981]
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Use the UUID as an argument to the SYSTEM$GET\_LOGIN\_FAILURE\_DETAILS function, and extract the error using the
   [JSON\_EXTRACT\_PATH\_TEXT](../sql-reference/functions/json_extract_path_text) function:

   > CopyExpand
   >
   > ```
   > SELECT JSON_EXTRACT_PATH_TEXT(SYSTEM$GET_LOGIN_FAILURE_DETAILS('eb55b777-50a4-4db5-b231-9ee457fb3981'), 'errorCode');
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Find the error description in the table below:

   > | Error Code | Error | Description |
   > | --- | --- | --- |
   > | 390133 | SAML\_RESPONSE\_INVALID | The SAML response was invalid for an unspecified reason, although it is most likely malformed (this is also used if there is an error on parsing). |
   > | 390165 | SAML\_RESPONSE\_INVALID\_SIGNATURE | The SAML response contains an invalid Signature. |
   > | 390166 | SAML\_RESPONSE\_INVALID\_DIGEST\_METHOD | The SAML response contains an invalid “DigestMethod” attribute or omits it entirely. |
   > | 390167 | SAML\_RESPONSE\_INVALID\_SIGNATURE\_METHOD | The SAML response contains an invalid “SignatureMethod” or omits it entirely. |
   > | 390168 | SAML\_RESPONSE\_INVALID\_DESTINATION | The “Destination” attribute in the SAML response does not match a valid destination URL on the account. |
   > | 390169 | SAML\_RESPONSE\_INVALID\_AUDIENCE | The SAML response does not contain exactly one audience or the audience URL does not match what we expect the audience URL to be. |
   > | 390170 | SAML\_RESPONSE\_INVALID\_MISSING\_INRESPONSETO | The “InResponseTo” attribute in the SAML assertion is missing. |
   > | 390171 | SAML\_RESPONSE\_INVALID\_RECIPIENT\_MISMATCH | The “Recipient” attribute does not match a valid destination URL. |
   > | 390172 | SAML\_RESPONSE\_INVALID\_NOTONORAFTER\_VALIDATION | This typically indicates that the time in which the SAML assertion is valid has expired. |
   > | 390173 | SAML\_RESPONSE\_INVALID\_NOTBEFORE\_VALIDATION | This typically indicates that the time in which the SAML assertion is valid has not yet come. |
   > | 390174 | SAML\_RESPONSE\_INVALID\_USERNAMES\_MISMATCH | The login names do not match during re-authentication. |
   > | 390175 | SAML\_RESPONSE\_INVALID\_SESSIONID\_MISSING | During re-authentication, we were unable to find a session corresponding to the user. |
   > | 390176 | SAML\_RESPONSE\_INVALID\_ACCOUNTS\_MISMATCH | During re-authentication, the names of the accounts were found to not match. |
   > | 390177 | SAML\_RESPONSE\_INVALID\_BAD\_CERT | The x.509 certificate contained in the SAML response is either malformed or does not match the expected certificate. |
   > | 390178 | SAML\_RESPONSE\_INVALID\_PROOF\_KEY\_MISMATCH | The proof keys do not match with respect to the authentication request ID. |
   > | 390179 | SAML\_RESPONSE\_INVALID\_INTEGRATION\_MISCONFIGURATION | The SAML IdP configuration is invalid. |
   > | 390180 | SAML\_RESPONSE\_INVALID\_REQUEST\_PAYLOAD | During authentication, using an invalid payload or using an invalid federated OAuth connection string. |
   > | 390181 | SAML\_RESPONSE\_INVALID\_MISSING\_SUBJECT\_CONFIRMATION\_BEARER | The Subject confirmation with Bearer method is missing and cannot be validated. |
   > | 390182 | SAML\_RESPONSE\_INVALID\_MISSING\_SUBJECT\_CONFIRMATION\_DATA | The Subject confirmation data is missing in the assertion. |
   > | 390183 | SAML\_RESPONSE\_INVALID\_CONDITIONS | The SAML assertion is not valid for a reason that is different than the preceding conditions in this table. |
   > | 390184 | SAML\_RESPONSE\_INVALID\_ISSUER | The SAML Response contained an issuer/entityID value different from the one configured in the SAML IDP Configuration. |
   >
   > See moreShow less
   >
   > Expand
