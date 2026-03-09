---
title: "Configure Snowflake OAuth for custom clients"
url: "https://docs.snowflake.com/en/user-guide/oauth-custom"
---

# Configure Snowflake OAuth for custom clients

This topic describes how to configure OAuth support for custom clients.

## Workflow

The following high-level steps are required to configure OAuth for custom clients:

1. Register your client with Snowflake. To register your client, create an integration. An integration is a Snowflake object that provides
   an interface between Snowflake and third-party services, such as a client that supports OAuth.

   The registration process defines a client ID and client secrets.
2. Configure calls to the Snowflake OAuth endpoints to request authorization codes from the Snowflake authorization server and to request
   and refresh access tokens.

   The optional “scope” parameters in the initial authorization request limit the role permitted by the access token and can additionally
   be used to configure the refresh token behavior.

Note

In-session role switching to secondary roles is not supported with Snowflake OAuth.

If this behavior is necessary with your OAuth workflow, use External OAuth instead.

For more information, see [Using secondary roles with External OAuth](oauth-ext-overview.html#label-ext-oauth-secondary-roles).

## Create a Snowflake OAuth integration

Create a Snowflake OAuth integration using the
[CREATE SECURITY INTEGRATION](../sql-reference/sql/create-security-integration-oauth-snowflake) command. Be sure to
specify `OAUTH_CLIENT = CUSTOM` when creating the integration.

Note

Only account administrators (users with the ACCOUNTADMIN role) or a role with the global CREATE INTEGRATION privilege can execute this
SQL command.

### Blocking specific roles from using the integration

The optional BLOCKED\_ROLES\_LIST parameter allows you to list Snowflake roles that a user cannot explicitly consent to using with
the integration.

By default, the ACCOUNTADMIN, SECURITYADMIN, GLOBALORGADMIN, and ORGADMIN roles are included in this list and cannot be removed. If you have a business
need to allow users to use Snowflake OAuth with these roles, and your security team allows it, please contact
[Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support) to request that these roles be allowed for your account.

### Using Client Redirect with Snowflake OAuth custom clients

Snowflake supports using Client Redirect with Snowflake OAuth Custom Clients, including using Client Redirect and OAuth with supported
Snowflake Clients.

For more information, see [Redirecting client connections](client-redirect).

### Managing network policies

Snowflake supports network policies for OAuth. For more information, see [Restricting network traffic for Snowflake OAuth](oauth-snowflake-overview.html#label-snow-oauth-network-policies).

### Integration example

The following example creates an OAuth integration that uses key pair authentication. The integration allows refresh tokens, which expire
after 1 day (86400 seconds). The integration blocks users from starting a session with SYSADMIN as the active role:

CopyExpand

```
CREATE SECURITY INTEGRATION oauth_kp_int
  TYPE = OAUTH
  ENABLED = TRUE
  OAUTH_CLIENT = CUSTOM
  OAUTH_CLIENT_TYPE = 'CONFIDENTIAL'
  OAUTH_REDIRECT_URI = 'https://localhost.com'
  OAUTH_ISSUE_REFRESH_TOKENS = TRUE
  OAUTH_REFRESH_TOKEN_VALIDITY = 86400
  BLOCKED_ROLES_LIST = ('SYSADMIN')
  OAUTH_CLIENT_RSA_PUBLIC_KEY ='
  MIIBI
  ...
  ';
```

Show lessSee more

Scroll to top

## Call the OAuth endpoints

OAuth endpoints are the URLs that clients call to request authorization codes and to request and refresh access tokens. These endpoints
refer to specific OAuth 2.0 policies that execute when the endpoint is called.

Snowflake provides the following OAuth endpoints:

Authorization:
:   `<snowflake_account_url>/oauth/authorize`

Token requests:
:   `<snowflake_account_url>/oauth/token-request`

Where `<snowflake_account_url>` is a valid Snowflake account URL. For example, you might use the endpoints
`https://myorg-account_xyz.snowflakecomputing.com/oauth/authorize` and
`https://myorg-account_xyz.snowflakecomputing.com/oauth/token-request`. For a list of supported formats for the Snowflake account URL,
see [Connecting with a URL](organizations-connect.html#label-connecting-via-url).

To see a list of valid OAuth endpoints for a security integration, execute [DESCRIBE INTEGRATION](../sql-reference/sql/desc-integration),
and then view the values in the `OAUTH_ALLOWED_AUTHORIZATION_ENDPOINTS` and `OAUTH_ALLOWED_TOKEN_ENDPOINTS` properties.

### Authorization endpoint

The authorization endpoint is used to obtain an authorization grant after a user successfully authorizes a client with Snowflake.

> Important
>
> The authorization endpoint must be opened in a browser that the user can interact with. Do not use cURL with this endpoint.

The authorization endpoint is as follows:

CopyExpand

```
<snowflake_account_url>/oauth/authorize
```

Show lessSee more

Scroll to top

Where:

> `snowflake_account_url`
> :   Specifies a valid [Snowflake account URL](organizations-connect.html#label-connecting-via-url). For example,
>     `https://myorg-account_xyz.snowflakecomputing.com/oauth/authorize`.

#### HTTP method

`GET`

#### Query parameters

Note

The following parameters should be URL encoded.

| Parameter | Data Type | Required? | Description |
| --- | --- | --- | --- |
| `client_id` | String | Yes | Client ID (provided by Snowflake when the client is registered) |
| `response_type` | String | Yes | Response type created. Currently supports `code` value, because Snowflake only issues authorization codes. |
| `redirect_uri` | String | Yes | URI where the user is redirected to after successfully authorizing. In general, this should match the value of the OAUTH\_REDIRECT\_URI parameter of the security integration.  However, if the `redirect_uri` includes query parameters, do not include those query parameters when defining the OAUTH\_REDIRECT\_URI parameter of the security integration. For example, if the value of the `redirect_uri` query parameter in the request to the authorization endpoint is `https://www.example.com/connect?authType=snowflake`, make sure the OAUTH\_REDIRECT\_URI parameter in the security integration is set to `https://www.example.com/connect`. |
| `state` | String | No | String of no more than 2048 ASCII characters that is returned with the response from the Snowflake authorization server. Typically used to prevent cross-site request forgery attacks. |
| `scope` | String | No | Space-delimited string that is used to limit the scope of the access request. For more information, refer to [Scope](#scope) (in this topic). |
| `code_challenge` | String | No | Challenge for Proof Key for Code Exchange (PKCE). String generated via a secret and a code challenge method. For more information, refer to [Proof key for code exchange](#label-proof-key-for-code-exchange) (in this topic). |
| `code_challenge_method` | String | No | String indicating the method used to derive the code challenge for PKCE. For more information, refer to [Proof key for code exchange](#label-proof-key-for-code-exchange) (in this topic). |

See moreShow less

Expand

When a user authorizes the client, a redirect is made to the `redirect_uri` that contains the following in a GET request:

> | Query Parameter | Description |
> | --- | --- |
> | `code` | Short-lived authorization code, which can be exchanged at the token endpoint for an access token. |
> | `state` | `state` value provided in the original request, unmodified. |
> | `scope` | Scope of the access request; currently the same as the `scope` value in the initial authorization request, but might differ in the future. For more information, see [Scope](#scope) (in this topic). |
>
> See moreShow less
>
> Expand

##### Scope

The `scope` query parameter in the initial authorization request optionally limits the operations and role permitted by the access token.

Scope is validated immediately when making an authorization request with respect to semantics, but not necessarily validity. That is, any
invalid scopes (e.g. “bogus\_scope”) are rejected before the user authenticates, but a scope the user does not have access to (a
particular role, etc.) does not result in an error until after the user authenticates.

The following are the possible values of the `scope` query parameter:

| Scope Value | Required? | Description |
| --- | --- | --- |
| `refresh_token` | No | If included in the authorization URL, Snowflake presents the user with the option to consent to offline access. In this context, offline access refers to allowing the client to refresh access tokens when the user is not present. With user consent, the authorization server returns a refresh token in addition to an access token when redeeming the authorization code. |
| `session:role:role_name` | No | Used to limit the access token to a single role that the user can consent to for the session. Only one session role scope can be specified. If this scope is omitted, then the default role for the user is used instead. When a user authorizes consent, Snowflake always displays the role for the session regardless if this scope is included in the authorization URL.  Note that `role_name` is case-sensitive and must be input in all uppercase unless the role name was enclosed in quotes when it was created using [CREATE ROLE](../sql-reference/sql/create-role). To verify the case, execute [SHOW ROLES](../sql-reference/sql/show-roles) in Snowflake and see the role name in the output.  If the role name contains characters that are reserved in a query parameter URL, you must use a `session:role-encoded:role_name` syntax, where `role_name` is a URL-encoded string. For example, if the role name is `AUTH SNOWFLAKE` (with a space), then the value of the `scope` query parameter must be `session:role-encoded:AUTH%20SNOWFLAKE`. |

See moreShow less

Expand

The following example limits authorization to the custom R1 role:

> CopyExpand
>
> ```
> scope=session:role:R1
> ```
>
> Show lessSee more
>
> Scroll to top

The following example indicates that access/refresh tokens should use the default role for the user and requests a refresh token so that
offline access can occur:

> CopyExpand
>
> ```
> scope=refresh_token
> ```
>
> Show lessSee more
>
> Scroll to top

The following example limits authorization to the custom R1 role and requests a refresh token so that offline access can occur:

> CopyExpand
>
> ```
> scope=refresh_token session:role:R1
> ```
>
> Show lessSee more
>
> Scroll to top

### Token endpoint

This endpoint returns access tokens or refresh tokens depending on the request parameters. The token endpoint is as follows:

CopyExpand

```
<snowflake_account_url>/oauth/token-request
```

Show lessSee more

Scroll to top

Where:

> `snowflake_account_url`
> :   Specifies a valid [Snowflake account URL](organizations-connect.html#label-connecting-via-url). For example,
>     `https://myorg-account_xyz.snowflakecomputing.com/oauth/token-request`.

#### HTTP method

`POST`

Ensure that the content-type header in the POST request is set as follows:

CopyExpand

```
Content-type: application/x-www-form-urlencoded
```

Show lessSee more

Scroll to top

#### Request header

The client ID and client secret must be included in the authorization header. Currently, Snowflake only supports the
[Basic Authentication Scheme](https://tools.ietf.org/html/rfc2617), which means that the value expected is in the following form:

`Basic Base64(client_id:client_secret)`

Where:

| Header Value | Data Type | Required | Description |
| --- | --- | --- | --- |
| `client_id` | String | Yes | Client ID of the integration. |
| `client_secret` | String | Yes | Client secret for the integration. |

See moreShow less

Expand

Both the client ID and client secret can be retrieved using the [SYSTEM$SHOW\_OAUTH\_CLIENT\_SECRETS](../sql-reference/functions/system_show_oauth_client_secrets) function.

Note the `:` character between `client_id` and `client_secret`.

#### Request body

| Parameter | Data Type | Required | Description |
| --- | --- | --- | --- |
| `grant_type` | String | Yes | Type of grant requested: . `authorization_code` indicates that an authorization code should be exchanged for an access token. . `refresh_token` indicates a request to refresh an access token. |
| `code` | String | Yes | Authorization code returned from the token endpoint. Used and required when `grant_type` is set to `authorization_code`. |
| `refresh_token` | String | Yes | Refresh token returned from an earlier request to the token endpoint when redeeming the authorization code. Used and required when `grant_type` is set to `refresh_token`. |
| `redirect_uri` | String | Yes | Redirect URI as used in the authorization URL when requesting an authorization code. Used and required when `grant_type` is set to `authorization_code`. |
| `code_verifier` | String | No | Required only if the authorization request was sent to the [Authorization Endpoint](#authorization-endpoint) with a `code_challenge` parameter value. Code verifier for PKCE. For more information, see [Proof key for code exchange](#label-proof-key-for-code-exchange) (in this topic). |

See moreShow less

Expand

#### Response

A JSON object is returned with the following fields:

| Field | Data Type | Description |
| --- | --- | --- |
| `access_token` | String | Access token used to establish a Snowflake session |
| `refresh_token` | String | Refresh token. Not issued if the client is configured to not issue refresh tokens or if the user did not consent to the `refresh_token` scope. |
| `expires_in` | Integer | Number of seconds remaining until the token expires |
| `token_type` | String | Access token type. Currently, always `Bearer`. |
| `username` | String | Username that the access token belongs to. Currently only returned when exchanging an authorization code for an access token. |

See moreShow less

Expand

##### Successful response example

The following example shows a successful response when exchanging an authorization code for an access and refresh token:

CopyExpand

```
{
  "access_token":  "ACCESS_TOKEN",
  "expires_in": 600,
  "refresh_token": "REFRESH_TOKEN",
  "token_type": "Bearer",
  "username": "user1",
}
```

Show lessSee more

Scroll to top

##### Unsuccessful response example

The following example shows an unsuccessful response:

CopyExpand

```
{
  "data" : null,
  "message" : "This is an invalid client.",
  "code" : null,
  "success" : false,
  "error" : "invalid_client"
}
```

Show lessSee more

Scroll to top

The `message` string value is a description of the error and `error` is the error type. For more information on the types of
errors returned, see [OAuth Error Codes](oauth-snowflake-overview.html#label-oauth-snowflake-error-codes).

### Token exchange

This endpoint returns an OAuth access token in exchange for a JSON Web Token (JWT). For an example, see [Tutorial 1 (step 5)](../developer-guide/snowpark-container-services/tutorials/tutorial-1). In the tutorial you send a request to this endpoint to exchange a JWT token for an OAuth token and use the OAuth token to access a public endpoint exposed by a Snowpark Container Services service.

The token endpoint is as follows:

CopyExpand

```
<snowflake_account_url>/oauth/token
```

Show lessSee more

Scroll to top

Where:

> `snowflake_account_url`
> :   Specifies a valid [Snowflake account URL](organizations-connect.html#label-connecting-via-url). For example,
>     `https://myorg-account_xyz.snowflakecomputing.com/oauth/token`.

#### HTTP method

`POST`

Ensure that the content-type header in the POST request is set as follows:

CopyExpand

```
Content-type: application/x-www-form-urlencoded
```

Show lessSee more

Scroll to top

#### Request body

| Parameter | Data Type | Required | Description |
| --- | --- | --- | --- |
| `grant_type` | String | Yes | Pass this as string `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `scope` | String | Yes | Pass this as string `session:role:role_name <ingress-endpoint-url>`. Note that the `role_name` is case sensitive. Use the [SHOW ENDPOINTS IN SERVICE](../sql-reference/sql/show-endpoints) command to find the ingress endpoint URL. . |
| `assertion` | String | Yes | Pass the JWT token. |

See moreShow less

Expand

For example,

CopyExpand

```
{
    'grant_type': 'urn:ietf:params:oauth:grant-type:jwt-bearer',
    'scope': 'session:role:TEST_ROLE ab12-orgname-acctname.snowflakecomputing.app',
    'assertion': '<token>'
}
```

Show lessSee more

Scroll to top

When specifying `scope`, the `session:role:role_name` is optional. If not provided, the default role of the user is used.

CopyExpand

```
{
    'grant_type': 'urn:ietf:params:oauth:grant-type:jwt-bearer',
    'scope': 'ab12-orgname-acctname.snowflakecomputing.app',
    'assertion': '<token>'
}
```

Show lessSee more

Scroll to top

#### Response

An OAuth access token is returned

## Proof key for code exchange

Snowflake supports Proof Key for Code Exchange (PKCE) for obtaining access tokens using the `authorization_code` grant type as
described in [RFC 7636](https://tools.ietf.org/html/rfc7636). PKCE can be used to lessen the possibility of an authorization code
interception attack, and is suitable for clients that might not be able to fully keep the client secret secure.

By default, PKCE is optional and is enforced only if the `code_challenge` and `code_challenge_method` parameters are both
included in the authorization endpoint URL. However, Snowflake highly recommends that your client require PKCE for all authorizations to
make the OAuth flow more secure.

The following describes how PKCE for Snowflake works:

1. The client creates a secret called the *code verifier* and performs a transformation on it to generate the *code challenge*. The client
   holds onto the secret.

   Important

   Generate the *code verifier* from the allowed ASCII characters according to
   [Section 4.1 of RFC 7636](https://tools.ietf.org/html/rfc7636#section-4.1).
2. The client directing the user to the Authorization URL appends the following two query parameters:

   `code_challenge`
   :   Specifies the code challenge generated in Step 1.

   `code_challenge_method`
   :   Specifies the transformations used on the code verifier in Step 1 to generate the code challenge. Currently, Snowflake only supports
       SHA256, so this value must be set to `S256`. The transformation algorithm for SHA256 is
       `BASE64URL-ENCODE(SHA256(ASCII(code_verifier)))`.
3. After the user consents to the requested scopes or Snowflake determines that consent is present for that user, the authorization code
   is issued.
4. The client receives the authorization code from the Snowflake authorization server, which it then submits along with the
   `code_verifier` in the request to the token endpoint.
5. Snowflake transforms the `code_verifier` value and verifies that the transformed value matches the `code_challenge` value
   used when generating authorizations. If these values match, then the authorization server issues the access and refresh tokens.

## Using key-pair authentication

Snowflake supports using key pair authentication rather than the typical username/password authentication when calling the OAuth token
endpoint. This authentication method requires a 2048-bit (minimum) RSA key pair. Generate the PEM (Privacy Enhanced Mail) public-private
key pair using OpenSSL. The public key is assigned to the Snowflake user who uses the Snowflake client.

To configure the public/private key pair:

1. From the command line in a terminal window, generate an encrypted private key:

   > CopyExpand
   >
   > ```
   > $ openssl genrsa 2048 | openssl pkcs8 -topk8 -v2 des3 -inform PEM -out rsa_key.p8
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

   OpenSSL prompts for a passphrase used to encrypt the private key file. Snowflake recommends using a strong passphrase to protect the private
   key. Record this passphrase. You must input it when connecting to Snowflake. Note that the passphrase is only used for protecting
   the private key and is never sent to Snowflake.

   **Sample PEM private key**

   > CopyExpand
   >
   > ```
   > -----BEGIN ENCRYPTED PRIVATE KEY-----
   > MIIE6TAbBgkqhkiG9w0BBQMwDgQILYPyCppzOwECAggABIIEyLiGSpeeGSe3xHP1
   > wHLjfCYycUPennlX2bd8yX8xOxGSGfvB+99+PmSlex0FmY9ov1J8H1H9Y3lMWXbL
   > ...
   > -----END ENCRYPTED PRIVATE KEY-----
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. From the command line, generate the public key by referencing the private key:

   > CopyExpand
   >
   > ```
   > $ openssl rsa -in rsa_key.p8 -pubout -out rsa_key.pub
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
   >
   > **Sample PEM public key**
   >
   > > CopyExpand
   > >
   > > ```
   > > -----BEGIN PUBLIC KEY-----
   > > MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAy+Fw2qv4Roud3l6tjPH4
   > > zxybHjmZ5rhtCz9jppCV8UTWvEXxa88IGRIHbJ/PwKW/mR8LXdfI7l/9vCMXX4mk
   > > ...
   > > -----END PUBLIC KEY-----
   > > ```
   > >
   > > Show lessSee more
   > >
   > > Scroll to top
3. Copy the public and private key files to a local directory for storage. Record the path to the files.

   Note that the private key is stored using the PKCS#8 (Public Key Cryptography Standards) format and is encrypted using the passphrase
   you specified in the previous step; however, the file should still be protected from unauthorized access using the file permission
   mechanism provided by your operating system. It is your responsibility to secure the file when it is not being used.
4. Assign the public key to the integration object using [ALTER SECURITY INTEGRATION](../sql-reference/sql/alter-security-integration-oauth-snowflake). For example:

   > CopyExpand
   >
   > ```
   > ALTER SECURITY INTEGRATION myint SET OAUTH_CLIENT_RSA_PUBLIC_KEY='MIIBIjANBgkqh...';
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

   Note

   * Only account administrators can execute the ALTER SECURITY INTEGRATION command.
   * Exclude the public key header and footer in the command.

   Verify the public key fingerprint using [DESCRIBE INTEGRATION](../sql-reference/sql/desc-integration):

   CopyExpand

   ```
   DESC SECURITY INTEGRATION myint;

   +----------------------------------+---------------+----------------------------------------------------------------------+------------------+
   | property                         | property_type | property_value                                                       | property_default |
   |----------------------------------+---------------+----------------------------------------------------------------------+------------------|
   ...
   | OAUTH_CLIENT_RSA_PUBLIC_KEY_FP   | String        | SHA256:MRItnbO/123abc/abcdefghijklmn12345678901234=                  |                  |
   | OAUTH_CLIENT_RSA_PUBLIC_KEY_2_FP | String        |                                                                      |                  |
   ...
   +----------------------------------+---------------+----------------------------------------------------------------------+------------------+
   ```

   Show lessSee more

   Scroll to top

   Note

   The `OAUTH_CLIENT_RSA_PUBLIC_KEY_2_FP` property is described in [Key Rotation](#key-rotation) (in this topic).
5. Modify and execute the sample code below. The code uses the private key to encode a JWT and then passes that token to the Snowflake
   authorization server:

   * Update the security parameters:

     + `<private_key>`: Contents of the decrypted `rsa_key.p8` (including BEGIN and END), which you can obtain by executing `openssl rsa -in rsa_key.p8 -text`.
   * Update the session parameters:

     + `<account_locator>`: Your account locator code, for example `CIB07125`.
       You cannot use the account name (for example, `myorg-account_xyz`).
   * Update the token-request endpoint in the `_make_request()` function:

     + `<account_name>`: This is the account name format of your account’s identifier (for example, `myorg-account_xyz`).
   * Update the public key fingerprint:

     + `<public_key_fp>`: Retrieved by executing the DESC SECURITY INTEGRATION command.
       There can be 2 public keys so ensure you’re referencing the correct key.
   * Update the redirect URI:

     + `<redirect_uri>`: The redirect URI your integration is configured with. Execute the DESC SECURITY INTEGRATION command to obtain it.
   * Obtain an OAuth authorization code:

     + `<oauth_az_code>`: Obtained after authenticating with your `/authorize` endpoint.
       Note: this code needs to be refreshed periodically.
   * Update the JSON Web Token (JWT) fields:

     post body
     :   A JSON object with the following standard fields (“claims”):

     | Attribute | Data Type | Required | Description |
     | --- | --- | --- | --- |
     | `iss` | String | Yes | Specifies the principal that issued the JWT in the format `client_id.public_key_fp` where `client_id` is the client ID of the OAuth client integration and `public_key_fp` is the fingerprint of the public key that is used during verification. |
     | `sub` | String | Yes | Subject of the JWT in the format `account_locator.client_id` where |
     | `account_locator` is your Snowflake account locator and `client_id` is the client ID of the OAuth client integration. Depending on the cloud platform (AWS or Azure) and region where your account is hosted, the full account name might require additional segments. For more information, see the `account` variable description under [Token endpoint](#label-oauth-token-endpoint). |  |  |  |
     | `iat` | Timestamp | No | Time when the token was issued. |
     | `exp` | Timestamp | Yes | Time when the token should expire. This period should be relatively short (e.g. a few minutes). |

     See moreShow less

     Expand

   **Sample code**

   > Note that the `private_key` value (decrypted) includes the `-----BEGIN` header and the `-----END` footer.
   >
   > CopyExpand
   >
   > ```
   > import datetime
   > import json
   > import urllib
   >
   > import jwt
   > import requests
   >
   > private_key = """
   > <private_key>
   > """
   >
   > public_key_fp = "<public_key_fp>" # SHA256:MR...
   >
   >
   > def _make_request(payload, encoded_jwt_token):
   >     token_url = "https://<account_name>.snowflakecomputing.com/oauth/token-request"
   >     headers = {
   >             u'Authorization': "Bearer %s" % (encoded_jwt_token),
   >             u'content-type': u'application/x-www-form-urlencoded'
   >     }
   >     r = requests.post(
   >             token_url,
   >             headers=headers,
   >             data=urllib.urlencode(payload))
   >     return r.json()
   >
   >
   > def make_request_for_access_token(oauth_az_code, encoded_jwt_token):
   >     """ Given an Authorization Code, make a request for an Access Token
   >     and a Refresh Token."""
   >     payload = {
   >         'grant_type': 'authorization_code',
   >         'code': oauth_az_code,
   >         'redirect_uri': <redirect_uri>
   >     }
   >     return _make_request(payload, encoded_jwt_token)
   >
   >
   > def make_request_for_refresh_token(refresh_token, encoded_jwt_token):
   >     """ Given a Refresh Token, make a request for another Access Token."""
   >     payload = {
   >         'grant_type': 'refresh_token',
   >         'refresh_token': refresh_token
   >     }
   >     return _make_request(payload, encoded_jwt_token)
   >
   >
   > def main():
   >     account_locator = "<account_locator>"
   >     client_id = "1234"  # found by running DESC SECURITY INTEGRATION
   >     issuer = "{}.{}".format(client_id, public_key_fp)
   >     subject = "{}.{}".format(account_locator, client_id)
   >     payload = {
   >         'iss': issuer,
   >         'sub': subject,
   >         'iat': datetime.datetime.utcnow(),
   >         'exp': datetime.datetime.utcnow() + datetime.timedelta(seconds=30)
   >     }
   >     encoded_jwt_token = jwt.encode(
   >             payload,
   >             private_key,
   >             algorithm='RS256')
   >
   >     data = make_request_for_access_token(<oauth_az_code>, encoded_jwt_token)
   >     refresh_token = data['refresh_token']
   >     data = make_request_for_refresh_token(refresh_token, encoded_jwt_token)
   >     access_token = data['access_token']
   >
   >
   > if __name__ == '__main__':
   >     main()
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

   After the token is created, submit it in requests to the token endpoint. Requests require the Bearer authorization format as the
   authorization header instead of the basic authorization format normally used for the client ID and client secret, as follows:

   CopyExpand

   ```
   "Authorization: Bearer JWT_TOKEN"
   ```

   Show lessSee more

   Scroll to top

### Key rotation

Snowflake supports multiple active keys to allow for uninterrupted rotation. Rotate and replace your public and private keys based on the
expiration schedule you follow internally.

Currently, you can use the `OAUTH_CLIENT_RSA_PUBLIC_KEY` and `OAUTH_CLIENT_RSA_PUBLIC_KEY_2` parameters for
[ALTER SECURITY INTEGRATION](../sql-reference/sql/alter-security-integration-oauth-snowflake) to associate up to 2 public keys with a single user.

To rotate your keys:

1. Complete the steps in [Using key-pair authentication](#using-key-pair-authentication) (in this topic):

   * Generate a new private and public key set.
   * Assign the public key to the integration. Set the public key value to either `OAUTH_CLIENT_RSA_PUBLIC_KEY` or
     `OAUTH_CLIENT_RSA_PUBLIC_KEY_2` (whichever key value is not currently in use). For example:

     > CopyExpand
     >
     > ```
     > alter integration myint set oauth_client_rsa_public_key_2='JERUEHtcve...';
     > ```
     >
     > Show lessSee more
     >
     > Scroll to top
2. Update the code to connect to Snowflake. Specify the new private key.

   Snowflake verifies the correct active public key for authentication based on the submitted private key.
3. Remove the old public key from the integration. For example:

   CopyExpand

   ```
   alter integration myint unset oauth_client_rsa_public_key;
   ```

   Show lessSee more

   Scroll to top

## Error codes

See the [Error codes](oauth-snowflake-overview.html#label-oauth-snowflake-error-codes) for a list of error codes associated with OAuth, as well as errors that are returned in the JSON
blob, during the authorization flow, token request or exchange, or when creating a Snowflake session after completing the OAuth flow.

## Pre-authorizing user consent for a role

Security administrators (i.e. users with the SECURITYADMIN role) or higher can pre-authorize consent for a client to initiate a session for
a user using a specified role and integration. This consent is granted using [ALTER USER](../sql-reference/sql/alter-user) with the ADD DELEGATED
AUTHORIZATION keywords. Without this delegated authorization, a user must authorize consent for the role after authentication. This
delegated authorization can also be revoked.

For more information, see [Managing user consent for OAuth](oauth-consent).
