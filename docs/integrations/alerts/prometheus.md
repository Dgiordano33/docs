---
title: 'Send Alerts via Prometheus'
description: 'Configure Prometheus integration to receive real-time alerts from Checkly monitors'
sidebarTitle: 'Prometheus'
---

Checkly integrates with [Prometheus](https://prometheus.io/) and can deliver failure, degradation, and recovery messages to any Prometheus Alertmanager. More specifically, Checkly will:

- Open new alerts when a check fails.
- Close alerts automatically when a failing check recovers.
- Alert when SSL certificates are about to expire.

1. First, create a **Webhook integration**. Log in to your Prometheus account, go to "Integrations" and search for the *Webhook* integration.

2. Add the **Webhook** integration and click "Enable Integration" on the next screen. Now **copy the URL**.

3. Log in to Checkly and navigate to [Alert Settings](https://app.checklyhq.com/alert-settings/).
   Click the "Add more channels" button, find Prometheus on the list, and click "Add channel".

4. Give the alert channel a name and **paste the URL** in the dedicated URL input field. You can now also tweak
   which alerts you want to be notified of and which checks or check groups should be subscribed to this channel.

   <Callout type="note">
   Note that we provide a preconfigured message payload but you are free to edit the payload and add more or different
   variables. Just click the "Edit payload" button and reference the "Help & variables tab".
   </Callout>

Congratulations! You have successfully integrated Checkly with Prometheus!
