# Create a Quality Check in Production

![image](https://user-images.githubusercontent.com/76087882/128972554-1ef85987-2e67-4553-97a5-12f5f4341aca.png)

Use these instructions to create a production quality check.

This quality check is designed to start the conversation with customers around our Innovation practices.

Once an hour this quality check will produce a report of production response times. This can be used as a conversation starter at your customer accounts to suggest that they use this information to inform their preproduction to production quality gate. We have removed the guess work. They can now implement quality gates in preproduction based on this information.

## Conditions & Warnings
By proceeding, you agreed to the following warnings and conditions:

1. The capability provided here is as a PoC instrument / conversation starter at your customer(s) only
1. Customers should not be given direct access to this PoC capability / UI. It is for your **internal** use only
1. Screenshots of the quality evaluations can (and should) be shared with the customer
3. If a customer requires this capability, please reach out to the Innovation lead in your geo
4. Evaluations will stop after **7 days**. The historic data, however, will remain

## 1. Create a Dashboard
Create a new dashboard on your tenant. Add only one metric per tile. Add a maximum of `3` tiles. It is recommended to use calculated service metrics.

For each tile, set the title to be: `sli={metricname}` like this:

![image](https://user-images.githubusercontent.com/76087882/128966704-e682bd85-4fe4-409a-b93d-4f52c92b75e6.png)

## 2. Dashboard ID

Save the dashboard ID from the URL. Here the ID is: `3ccb5be7-4911-497a-a514-cd8ae2beb650`

![image](https://user-images.githubusercontent.com/76087882/128966811-cb9e5943-84a9-402a-8181-53dbb336315e.png)

## 3. Create a Dynatrace API Token

Create an API token with two permissions:

1. Read Metrics
2. Read Configuration

![image](https://user-images.githubusercontent.com/76087882/128966956-f41438e0-98f7-4612-b035-5a2ca3cd2ef8.png)

## 4. Gather Details

Gather the following details:

1. Dynatrace environment URL: eg. `https://abc123.live.dynatrace.com`
2. Dynatrace API Token: From above...
3. Dynatrace dashboard ID: From above...
4. Your Initials
5. Customer name

# 5. Submit Details

Send an email to `services.apac@dynatrace.com`. Your quality gate will be created and you will receive login information within one business day.
