PoP Token Builder
=================

Build/Install PoP Token Builder Library
---------------------------------------

Execute following command to build the PoP token builder library.

::

   mvn clean install

Implementation Details
----------------------

The PoP Token Builder library follows the following logic for
creating the PoP token.

-  Sets up the edts (external data to sign) / ehts (external headers to
   sign) claims in PoP token using the specified ehts key-value map. The
   library uses SHA256 algorithm for calculating the edts and then the
   final edts value is encoded using Base64 URL encoding.
-  Signs the PoP token using the specified RSA private key.
-  Creates the PoP token with 2 minutes of validity period.
-  Current PoP token builder and validator libraries support RSA PKCS8
   format key for signing and validating the PoP tokens.

**Notes:**

-  PopTokenBuilder is not thread-safe so the instance of this class
   should not be shared with between multiple threads.
-  If the API request body is very large or data is being streamed then
   the “body” should not be added to ehts/edts while building/validating
   the PoP token.

Determining the ehts Key Name
-----------------------------

-  For HTTP request URI, “uri” should be used as ehts key name,
   ``PopEhtsKey.URI.keyName()``. For “uri” ehts value, the URI and query
   string of the request URL should be put in the ehts key-value map.
   Example: If the URL is
   ``https://api.com/commerce/v1/orders?account-number=0000000000``
   then only ``/commerce/v1/orders?account-number=0000000000`` should be
   used as ehts value. The query parameter values part of “uri” ehts
   value should not be in URL encoded format.
-  For HTTP method, “http-method” should be used as ehts key name,
   ``PopEhtsKey.HTTP_METHOD.keyName()``.
-  For HTTP request headers, the header name should be used as ehts key
   name.
-  For HTTP request body, “body” should be used as ehts key name,
   ``PopEhtsKey.BODY.keyName()``.

Supported Key Format
--------------------

PoP token builder and validator libraries are currently supporting PKCS8
key format.

**Using Non Encrypted Keys:**

Below commands shows how to create private and public keys in PKCS8
format:

::

   # Creates private key in PKCS1 format
   openssl genrsa -out private-key-pkcs1.pem 2048

   # Converts private key to PKCS8 format
   openssl pkcs8 -topk8 -inform PEM -in private-key-pkcs1.pem -outform PEM -nocrypt -out private-key-pkcs8.pem

   # Creates public key in PKCS8 format
   openssl rsa -in private-key-pkcs8.pem -outform PEM -pubout -out public-key.pem

**Using AES256 Encrypted Keys:**

Below commands shows how to create AES256 encrypted private key and
public key in PCS8 format:

\``\` # Creates encrypted private key in PKCS1 format openssl genrsa
-aes256 -passout pass:foobar -out private_key_aes256_with_password.pem
2048

Converts private key to PKCS8 format
====================================

openssl pkcs8 -topk8 -inform PEM -in
private_key_aes256_with_password.pem -outform PEM -out
private_key_aes256_with_password_pkcs8.pem -passin pass:foobar -passout
pass:foobar

Creates public key
==================

openssl rsa -in private_key_aes