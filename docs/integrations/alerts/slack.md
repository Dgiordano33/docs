---
title: 'Sending Alerts to Slack'
description: 'Configure Slack integration to receive real-time alerts from Checkly monitors'
sidebarTitle: 'Slack'
---

Setting up Slack as an alerting channel is simple:

1. [Create an Incoming WebHook integration](https://my.slack.com/services/new/incoming-webhook/) in Slack by selecting a default channel for your alerts. You'll see a WebHook URL generated by Slack. Copy it.
2. In Checkly, go to [Alert Settings -> Add More Channels -> Slack](https://app.checklyhq.com/alert-settings/channels/new/slack/) and paste your URL to add it as an alerting channel:

![Add a Slack WebHook URL to Checkly](/docs/images/alerting/slack.png)

You have the option to customize the default channel you've set in Slack by setting a channel name while creating your Alert Channel. You can use a **#public-channel-name**, **@username** for Direct Messages or a channel ID. If you omit this field, the message will arrive in the default channel you've picked while creating the Incoming WebHook URL in Slack.

By customizing the channel name, alerting options & checks for that alert channel, you can use a single Incoming WebHook URL for multiple scenarios.

<Callout type="warning">
If you're using a free Slack workspace, be aware of [message limits](https://slack.com/help/articles/115002422943-Usage-limits-for-free-workspaces). Exceeding these limits may prevent new alerts from being delivered.
</Callout>

## Example Failed Slack Alert

We provide a lot of information in the initial Slack message including links to the check and check result as well as the whole response body.

![A slack message showing a failed alert](/docs/images/alerting/slack-failed-check.png)

## Example Recovered Slack Alert

From the recovered Slack message, you can see the timestamp as well as a link to the check itself.

![A slack message showing a recovered alert](/docs/images/alerting/slack-recovered-check.png)

## Custom Slack Webhook Integration

For users who need more control over the Slack alert format and content, you can use a **Webhook Alert Channel** in Checkly to send fully customized messages to Slack.

This approach is ideal when the native Slack integration does not meet your formatting or dynamic content needs. You will be using a Webhook alert channel with a Slack Incoming WebHook URL and a custom payload template. This setup allows you to control Slack message formatting using Slack's [Block Kit](https://api.slack.com/block-kit).

### Steps

1. **Create a Slack Webhook**
   - [Create an Incoming WebHook integration](https://my.slack.com/services/new/incoming-webhook/) in Slack by selecting a default channel for your alerts. You'll see a WebHook URL generated by Slack. Copy it.
   - Choose the bot name and icon for your alerts.

2. **Create a Webhook Alert Channel in Checkly**
   - Go to [Alert Settings > Add More Channels > Webhook](https://app.checklyhq.com/alerts/settings/channels/new/webhook)
   - Fill in the following:
     - **Name**: e.g. `Custom Slack Alerts`
     - **Method**: `POST`
     - **URL**: Paste the Slack Webhook URL
     - **Notification events**: Enable recovery, degraded, and failure. SSL expiry optional, but not supported in this template.
     - **Body**: See template below

3. **Customize the Template**

The following template uses conditional logic (`{{#if}}`) to change the message depending on the alert type:

```json
{
  "attachments": [
    {
      {{#if (eq ALERT_TYPE "ALERT_FAILURE")}}
      "color": "#a30200",
      {{/if}}
      {{#if (eq ALERT_TYPE "ALERT_RECOVERY")}}
      "color": "#2eb886",
      {{/if}}
      {{#if (eq ALERT_TYPE "ALERT_DEGRADED_RECOVERY")}}
      "color": "#2eb886",
      {{/if}}
      {{#if (eq ALERT_TYPE "ALERT_DEGRADED")}}
      "color": "#daa038",
      {{/if}}
      "blocks": [
        {
          "type": "header",
          "text": {
            "type": "plain_text",
            "text": "Check Result Details",
            "emoji": true
          }
        },
        {
          "type": "section",
          "fields": [
            {
              "type": "mrkdwn",
              "text": "*Check:*\n<https://app.checklyhq.com/checks/{{CHECK_ID}}|{{CHECK_NAME}}>"
            },
            {{#if GROUP_NAME}}
            {
              "type": "mrkdwn",
              "text": "*Group:*\n{{GROUP_NAME}}"
            },
            {{/if}}
            {
              "type": "mrkdwn",
              "text": "*Status:*\n{{ALERT_TYPE}}"
            },
            {
              "type": "mrkdwn",
              "text": "*Location:*\n{{LOCATION}}"
            }
          ]
        },
        {
          "type": "section",
          "fields": [
            {
              "type": "mrkdwn",
              "text": "*Response Time:*\n{{RESPONSE_TIME}}ms"
            },
            {
              "type": "mrkdwn",
              "text": "*Timestamp:*\n{{TIMESTAMP}}"
            }
          ]
        },
        {{#if (eq ALERT_TYPE "ALERT_FAILURE")}}
        {
          "type": "section",
          "text": {
            "type": "mrkdwn",
            "text": "*Error Details:*\n{{ERROR_MESSAGE}}"
          }
        },
        {{/if}}
        {
          "type": "actions",
          "elements": [
            {
              "type": "button",
              "text": {
                "type": "plain_text",
                "text": "View Check",
                "emoji": true
              },
              "url": "https://app.checklyhq.com/checks/{{CHECK_ID}}"
            },
            {
              "type": "button",
              "text": {
                "type": "plain_text",
                "text": "View Result",
                "emoji": true
              },
              "url": "https://app.checklyhq.com/checks/{{CHECK_ID}}/results/{{RESULT_ID}}"
            }
          ]
        }
      ]
    }
  ]
}
```

### Template Variables

The template uses these Checkly variables:

- `{{CHECK_ID}}` - Unique identifier for the check
- `{{CHECK_NAME}}` - Name of the check
- `{{GROUP_NAME}}` - Name of the check group (if applicable)
- `{{ALERT_TYPE}}` - Type of alert (ALERT_FAILURE, ALERT_RECOVERY, etc.)
- `{{LOCATION}}` - Monitoring location where the check ran
- `{{RESPONSE_TIME}}` - Response time in milliseconds
- `{{TIMESTAMP}}` - When the check was executed
- `{{ERROR_MESSAGE}}` - Error details (for failures)
- `{{RESULT_ID}}` - Unique identifier for the check result

### Customization Options

You can further customize the template by:

- Adding more fields to display additional information
- Changing colors based on alert severity
- Including custom emojis or formatting
- Adding conditional blocks for different alert types
- Including links to your internal systems or dashboards

This custom webhook approach gives you complete control over the Slack message format while maintaining all the functionality of Checkly's native Slack integration.
