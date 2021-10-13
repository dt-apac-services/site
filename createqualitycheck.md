# Create a Quality Check in Production

![image](https://user-images.githubusercontent.com/76087882/128972554-1ef85987-2e67-4553-97a5-12f5f4341aca.png)

Use these instructions to create a production quality check.

This quality check is designed to start the conversation with customers around our Innovation practices.

Once an hour this quality check will produce a report of production response times. This can be used as a conversation starter at your customer accounts to suggest that they use this information to inform their preproduction to production quality gate. We have removed the guess work. They can now implement quality gates in preproduction based on this information.

## Conditions & Warnings
By proceeding, you agree to the following warnings and conditions:

1. Use existing metrics in a customer environment. Creating calculated service metrics will consume DDU.
1. The capability provided here should only be used as a PoC instrument / conversation starter at your customer(s).
1. Customers should not be given direct access to this UI. It is for your **internal** use only.
1. Screenshots of the quality evaluations can (and should) be shared with the customer.
1. If a customer requires this capability, please reach out to the Innovation lead in your geo.
1. Evaluations will stop after **7 days**. The historic data, however, will remain.

## 1. Create a Dynatrace API Token

Create an API token with API v2 permissions `Read Metrics`

![image](https://user-images.githubusercontent.com/76087882/137074707-8199c68e-c6d8-40ad-8097-f3c3990775b8.png)


## 2. Gather Data Explorer Code
Chart your metrics in the data explorer and copy the output from the `code` tab.
Complete this Excel and send an email to `services.apac@dynatrace.com`. Attach the following details and the Excel sheet:

1. Dynatrace environment URL: eg. `https://abc123.live.dynatrace.com`
2. Dynatrace API Token: From above...
3. Your Initials
4. Customer name

Your quality gate will be created and you will receive login information within one business day.

## 3. Arrange Customer Call
Arrange a call with your customer for the Innovation team to discuss the results and next steps.
