# Amazon Pinpoint

[Back to index](../../README.md)

The source describes Amazon Pinpoint for targeted marketing across email, SMS, and push notifications. A **journey** defines a sequence of engagement steps, such as sending an SMS and following up after **two days** if a customer has not responded. These are source-era capabilities; availability must be checked for a new deployment.

## Notes

### Two-way SMS and event analysis

For a mobile-app campaign that sends a confirmation SMS and accepts subscriber replies, the source uses a Pinpoint journey with event streaming to **Kinesis Data Streams** for near-real-time collection, processing, and analysis.

When responses must be retained for **one year**, configure stream retention to **365 days** rather than relying on the **24-hour default**. The documented maximum retention in the source is **365 days**. Ensure the chosen two-way SMS and event integration carries the required reply data into the analysis stream.

Pinpoint's journey and targeting features provide campaign orchestration. SNS SMS publishing serves a different purpose and does not itself provide the described journey and reply-analysis workflow.
