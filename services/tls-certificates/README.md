# TLS Certificates

[Back to index](../../README.md)

## Notes

### Multiple independent domains on one ALB

**Server Name Indication (SNI)** lets an ALB select the appropriate TLS certificate using the hostname sent by the client during the TLS handshake. Attach multiple certificates to the same HTTPS listener on **port 443**.

For separate domains such as `i-love-manila.com`, `i-love-boracay.com`, and `i-love-cebu.com`, add each domain’s certificate to the listener. Adding another domain, such as `i-love-hanoi.com`, does not require reissuing the certificates for existing domains. ALB does not charge an additional fee specifically for SNI certificate selection.

### Certificate alternatives

- A **Subject Alternative Name (SAN)** certificate can cover multiple domain names in one certificate. Changing the covered names requires a replacement certificate and validation of the new names.
- A **wildcard certificate**, such as `*.i-love-philippines.com`, covers matching subdomains such as `blog.i-love-philippines.com` and `news.i-love-philippines.com`. It does not cover arbitrary unrelated domains.
- **CloudFront dedicated-IP custom SSL** is a different, higher-cost option. The source cites approximately **$600 per month per certificate**; treat that figure as source-era pricing rather than a verified current quote.
