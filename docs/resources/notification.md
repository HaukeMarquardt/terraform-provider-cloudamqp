---
layout: "cloudamqp"
page_title: "CloudAMQP: cloudamqp_notification"
description: |-
  Creates and manages recipients to receive alarm notifications.
---

# cloudamqp_notification

This resource allows you to create and manage recipients to receive alarm notifications. There will always be a default recipient created upon instance creation. This recipient will use team email and receive notifications from default alarms.

Available for all subscription plans.

## Example Usage

```hcl
# New recipient to receieve notifications
resource "cloudamqp_notification" "email_recipient" {
  instance_id = cloudamqp_instance.instance.id
  type        = "email"
  value       = "alarm@example.com"
  name        = "alarm"
}

resource "cloudamqp_notification" "victorops_recipient" {
  instance_id = cloudamqp_instance.instance.id
  type        = "victorops"
  value       = "<UUID>"
  name        = "Victorops"
  options     = {
    "rk" = "ROUTINGKEY"
  }
}

resource "cloudamqp_notification" "pagerduty_recipient" {
  instance_id = cloudamqp_instance.instance.id
  type        = "pagerduty"
  value       = "<integration-key>"
  name        = "PagerDuty"
  options     = {
    "dedupkey" = "DEDUPKEY"
  }
}
```

## Argument Reference

The following arguments are supported:

* `instance_id` - (Required) The CloudAMQP instance ID.
* `type`        - (Required) Type of the notification. See valid options below.
* `value`       - (Required) Integration/API key or endpoint to send the notification.
* `name`        - (Optional) Display name of the recipient.
* `options`     - (Optional) Options argument (e.g. `rk` used for VictorOps routing key).

## Attributes Reference

All attributes reference are computed

* `id`  - The identifier for this resource.

## Notification Type reference

Valid options for notification type.

* email
* webhook
* pagerduty
* victorops
* opsgenie
* opsgenie-eu
* slack
* teams

## Options parameter

| Type      | Options  | Description                                                                                                                                                                                                                                                                      | Note                                                                                                                                    |
|-----------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| Victorops | rk       | Routing key to route alarm notification                                                                                                                                                                                                                                          | -                                                                                                                                        |
| PagerDuty | dedupkey | Default the dedup key for PagerDuty is generated depending on what alarm has triggered, but here you can set what `dedup` key to use so even if the same alarm is triggered for different resources you only get one notification. Leave blank to use the generated dedup key. | If multiple alarms are triggered using this recipient, since they all share `dedup` key only the first alarm will be shown in PagerDuty |

## Dependency

This resource depends on CloudAMQP instance identifier, `cloudamqp_instance.instance.id`.

## Import

`cloudamqp_notification` can be imported using CloudAMQP internal identifier of a recipient together (CSV separated) with the instance identifier. To retrieve the identifier of a recipient, use [CloudAMQP API](https://docs.cloudamqp.com/cloudamqp_api.html#list-notification-recipients)

`terraform import cloudamqp_notification.recipient <id>,<instance_id>`
