---
layout: twoColumn
section: Bank transfer workflow
type: article
title:  "Processing times"
weight: 0
description: "Processing times for Dwolla's bank transfer API to programmatically send money online."
---

# Bank transfer workflow

## Bank-to-Bank Transfers

When utilizing Dwolla to facilitate bank-to-bank transfers, you can see that there are two steps involved in the movement of funds between banks:

* [ACH Payment In](/resources/bank-transfer-workflow/processing-times.html#bank-to-dwolla-ach-payment-in) - Funds moving from the source bank account into the Dwolla Network.
* [ACH Payment Out](/resources/bank-transfer-workflow/processing-times.html#dwolla-network-to-bank-ach-payment-out) - Funds moving into a destination bank account from the Dwolla Network.
  
Let's illustrate movement of funds for a bank to bank transfer in the diagram below.

![Transfer timing](/images/ACH_Transfer-timeline.png "Dwolla ACH transfer timeline")

## Bank to Dwolla (ACH Payment In)

Transfers into the Dwolla network from a bank, including transfers that pass through the network (balance) to another Customer’s bank account, can be cancelled at any point until 4pm CT on that same business day if initiated prior to 4PM CT. If a transfer was initiated after 4pm CT, it can usually be cancelled anytime before 4pm CT on the following business day. To determine if a transfer is eligible for cancellation, Dwolla will return a cancel link on the [transfer resource](https://docs.dwolla.com/#cancel-a-transfer)

![Transfer timing](/images/ACH_Next-Day.png "Dwolla ACH transfer timing, pay in")

##### ACH Payment In - Standard clearing

Standard transfers from a bank to the Dwolla network will take 3-4 business days to settle. ACH is known as a next-business day settlement system, however, to account for ACH returns and processing delays by financial institutions the processing timeline is extended. With this ACH processing, funds being held for a few days which allows for checks on common return codes; including insufficient funds, and basic checks for valid bank accounts. If during ACH processing a failure is discovered as funds are held, the transfer will fail and be marked with a return code. To learn more about the various ACH return codes that can be returned systematically on a transfer failure, take a look at our [transfer failures developer resource article.](https://developers.dwolla.com/resources/bank-transfer-workflow/transfer-failures.html)

##### ACH Payment In - Next-Day clearing

Dwolla offers expedited clearing into the Dwolla network from a bank account. Using `next-day` transfers, funds will be made available in the Dwolla Network 1-2 business days after the transfer is created.
Next-Day transfers are an account setting that can be enabled on account-level basis for you and users that send funds on your application. **After receiving proper approval from Dwolla, we will enable transfers into the Dwolla network as `next-day`.** In the case where you would want to slow down the availability of incoming funds to standard ACH, you will need to specify this in the API transfer request. To learn more on how to set transfers to the default standard processing, refer to the `clearing` object in our [API Reference Docs](https://docs.dwolla.com/#clearing-json-object).

<ol class = "alerts">
  <li class = "alert icon-alert-alert">
    With `next-day` enabled, ACH processing is expedited, meaning that checks for common return codes may not be processed until a later time. This also means that the risk of losses incurred from return codes is higher.
  </li>
</ol>

## Dwolla network to bank (ACH Payment Out)

Transfers out of the Dwolla network can be cancelled at any point until 4pm CT on that same business day if the transfer was initiated prior to 4PM CT. If a transfer was initiated after 4pm CT, it can usually be cancelled anytime before 4pm CT on the following business day. To determine if a transfer is eligible for cancellation, Dwolla will return a cancel link on the [transfer resource](https://docs.dwolla.com/#cancel-a-transfer)

![Transfer timing](/images/ACH_Same-Day.png "Dwolla ACH transfer timing, pay out")

##### ACH Payment Out - Standard clearing

Standard clearing time out of the Dwolla Network to a bank takes 1-2 business days to settle. If a transfer is created prior to our 4 PM cutoff time, the funds will be sent through the ACH network for processing. Transfers created after our 4 PM cutoff time will result in the funds being processed out of the Dwolla network the following day.

##### ACH Payment Out - Same-Day clearing

Upgrading exports out of the Dwolla Network to a bank  using `Same-day` clearing will push funds to the `destination` bank in the same day. For instance, funds are moving out of the Dwolla network prior to 12:00PM Central Time will be available in the recipient’s bank account by the end of the day.
To learn more about how to initiate a Same-Day ACH credit transfer, reference our [developer resource article.](/resources/same-day-ach.html)

### Clearing example

In this bank-to-bank transfer example we have `next-day` enabled, but we want to slow the transfer clearing into the Dwolla network back to `standard`. However, we feel comfortable letting this transfer export out of the Dwolla Network with `same-day` clearing to the destination bank.

```noselect
{
    "_links": {
        "source": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/9615fc1d-8855-4ac2-ab11-e5469c66c8e8"
        },
        "destination": {
            "href": "https://api-sandbox.dwolla.com/funding-sources/b2d83ee5-5834-4cb7-9820-98149aff5063"
        }
    },
    "amount": {
        "currency": "USD",
        "value": "10.00"
    },
    "clearing": {
        "source": "standard",
        "destination": "next-available"
    }
}
```

## Total Time to `Processed`

The scenarios in the table below outline the total processing time for a given bank-to-bank transfer.

#### Customer to customer bank transfers

| Bank to Dwolla Network Clearing  | Dwolla Network to bank clearing | Time to Dwolla network | Time to destination bank  | Total time to `processed` |
|:------------------------|:------------------------|:-----------------------|---------------------------|:--------------------------|
|     Standard            | Standard                | 3-4 business days      |   1-2 business days       |     4-6 business days     |
|     Standard            | Same-day                | 3-4 business days      |   0-1 business days       |     3-5 business days     |
|     Next-day            | Standard                | 1-2 business days      |   1-2 business days       |     2-4 business days     |
|     Next-day            | Same-day                | 1-2 business days      |   0-1 business days       |     1-3 business days     |