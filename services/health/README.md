# AWS Health

[Back to index](../../README.md)

## Notes

### Upcoming events affecting your resources

The account-specific **AWS Health Dashboard**, previously called the Personal Health Dashboard, identifies AWS events affecting your resources. For example, scheduled hardware maintenance may identify the EC2 instance IDs that require an upcoming restart.

Route AWS Health events through **EventBridge** to an **SNS** topic to notify administrators by email or other configured subscriptions.

The public **Service Health Dashboard** reports broader service and Region health, such as a network incident in `us-east-1`; it does not provide account-specific EC2 instance details.

EC2 state-change events report changes such as an instance stopping or restarting. Use the Health event for advance notice of scheduled maintenance rather than waiting for the state change.
