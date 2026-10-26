# CloudFront

[Back to index](../../README.md)

## Notes

### Request and response processing with Lambda@Edge

Lambda@Edge runs custom logic at CloudFront edge locations without managing EC2 servers.

- **Image-format selection:** At a viewer-request or origin-request event, inspect the `User-Agent` or `Accept` header. Rewrite the URI to request an existing `.webp` object from S3 for a browser that supports WebP, or retain the `.jpg` path otherwise.
- **Response headers:** At an origin-response or viewer-response event, add a custom header such as `X-Tracking-ID` to the response.
- **Operational effort:** AWS runs the function at edge locations; you do not manage an image-processing server fleet, scaling, operating-system patches, or server maintenance.

A response headers policy handles configured HTTP headers, such as CORS and security headers. It does not implement conditional image-selection logic. Separate distributions and Route 53 records are unnecessary for selecting an image format based on request headers; DNS routing does not inspect `User-Agent`.

### Origin failover

A CloudFront origin group contains a **primary origin** and a **secondary origin**. CloudFront uses the primary and fails over to the secondary for configured failure conditions, such as connection failures, timeouts, or selected HTTP errors including **500, 502, 503, and 504**.

For the source’s EC2-origin availability example, place **two EC2 origins in different AZs** in the origin group. An Auto Scaling group cannot itself be a CloudFront origin. For a fleet of instances behind an ALB, configure the **ALB** as the origin.

### Global delivery and authentication

For an application with static content in **S3** and dynamic content behind an **ALB**, configure both as CloudFront origins, use appropriate cache behaviors, and point the **Route 53** domain record to the distribution. This supplies one global delivery layer for both content paths.

CloudFront can provide this HTTP delivery layer without adding Global Accelerator solely for that role. The appropriate choice depends on the workload's delivery and networking requirements.

The source’s slow-login scenario uses **Lambda@Edge** to execute suitable authentication logic closer to users and **origin failover** to handle selected origin errors, such as HTTP 504. Increasing static-content cache lifetime alone does not address login processing.

### Custom errors and restricting load-balancer access

To show a static page when an ALB-backed website is unavailable, point a Route 53 alias at CloudFront, use the ALB as the origin, and configure **custom error responses/pages**.

For a design requiring every user request to pass through CloudFront, restrict direct access to the origin as well as configuring the distribution. The source uses a **load-balancer security group** allowing CloudFront origin-facing service IP ranges and a **Lambda function** that updates those ranges as they change. Signed URLs or cookies control viewer access to CloudFront content; they do not by themselves prevent clients from reaching a public load-balancer origin. An S3 origin access identity does not protect an ELB origin.
