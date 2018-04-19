
#barion-nodejs

**barion-nodejs** is a compact Node.js module to manage online e-money and card payments via the *Barion Smart Gateway*.
It allows you to accept credit card and e-money payments in just a few lines of code.

**barion-nodejs** lets you
 * Start an online immediate or reservation payment easily
 * Get details about a given payment
 * Finish an ongoing reservation payment completely or partially, with automatic refund support
 * Refund a completed payment transaction completely or partially

All with just a few simple pieces of code!

## Installation

Using npm:
```shell
$ npm i -g npm
$ npm i --save barion-nodejs
```

In Node.js:
```js
// Create barion object for the prod environment.
var barion = new (require('barion-nodejs'))();
// Create barion object for the test environment.
var barion = new (require('barion-nodejs'))(BarionTest);

// Import BarionError
var BarionError = barion.BarionError;

// Import the built-in requestbuilders
var BarionRequestBuilderFactory = barion.BarionRequestBuilderFactory;
```

## Code Example

You can user the methods using simple Javascript objects and with the built-in request and object generators. We will give example for both.

### Error handling

<details>
<summary>Basic error handling</summary>
The library uses the default Node.js callback format with extension for BarionErrors. See the following example for error handling:

```js
function handleError(err) {
    if (err instanceof BarionError) {
        // The error comes from the Barion API
    } else {
        // The error comes from somewhere else (I/O, Barion Server, ... )
    }
}
```

</details>

### Money transfer

<details>
<summary>Send money using objects</summary>

```js
var transferSendRequestBuilder =  new BarionRequestBuilderFactory.BarionTransferSendRequestBuilder();

var moneySendOptionsWithObject = {
    UserName    : "my_barion_user@gmail.com",
    Password    : "myweakpassword",
    Recipient   : "my_buddys_barion_user@gmail.com",
    Currency    : "HUF",
    Amount      : 100,
    Comment     : "You are the best, pal!"
};

barion.sendMoney(moneySendOptionsWithObject, function(err, data) {
    if(!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

<details>
<summary>Send money using request builders</summary>

```js
var transferSendRequestBuilder =  new BarionRequestBuilderFactory.BarionTransferSendRequestBuilder();

var moneySendOptionsWithBuilder = transferSendRequestBuilder
    .setUsername("my_barion_user@gmail.com")
    .setPassword("myweakpassword")
    .setRecipient("my_buddys_barion_user@gmail.com")
    .setCurrency("HUF")
    .setAmount(100)
    .setComment("You are the best, pal!")
    .build();

barion.sendMoney(moneySendOptionsWithBuilder, function(err, data) {
    if(!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

### Start payment

<details>
<summary>Start payment using objects</summary>

```js
var paymentStartRequestBuilder  = new BarionRequestBuilderFactory.BarionPaymentStartRequestBuilder();

var paymentStartOptionsWithObject = {
    POSKey: "my_shops_pos_key_from_barion",
    PaymentType: "Immediate",
    GuestCheckOut: true,
    FundingSources: ["All"],
    PaymentRequestId: "request_id_generated_by_the_shop",
    Locale: "hu-HU",
    Currency: "HUF",
    Transactions: [
        {
            POSTransactionId: "test_payment_id_from_shop",
            Payee: "my_barion_user@gmail.com",
            Total: "1000",
            Items: [
                {
                    Name: "Test product",
                    Description: "My favorite test product",
                    Quantity: 1,
                    Unit: "db",
                    UnitPrice: 1000,
                    ItemTotal: 1000
                }
            ]
        }
    ]
};

barion.startPayment(paymentStartOptionsWithObject, function (err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

<details>
<summary>Start payment using request builders</summary>

```js
var paymentStartRequestBuilder  = new BarionRequestBuilderFactory.BarionPaymentStartRequestBuilder();
var paymentTransactionBuilder   = new paymentStartRequestBuilder.BarionPaymentTransactionBuilder();
var itemBuilder                 = new paymentStartRequestBuilder.BarionItemBuilder();

var item = itemBuilder
    .setName('Test product')
    .setDescription('My favorite test product')
    .setQuantity(1)
    .setUnit('db')
    .setUnitPrice(1000)
    .setItemTotal(1000)
    .build();

var paymentTransaction = paymentTransactionBuilder
    .setPOSTransactionId('test_payment_id_from_shop')
    .setPayee('my_barion_user@gmail.com')
    .setTotal(1000)
    .addItem(item)
    .build();

var paymentStartOptionsWithBuilder = paymentStartRequestBuilder
    .setPOSKey('my_shops_pos_key_from_barion')
    .setPaymentType('Immediate')
    .setGuestCheckout(true)
    .setFundingSources(["All"])
    .setPaymentRequestId('request_id_generated_by_the_shop')
    .setLocale('hu-HU')
    .setCurrency('HUF')
    .addTransaction(paymentTransaction)
    .build();

barion.startPayment(paymentStartOptionsWithBuilder, function (err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

### Get state of a payment

<details>
<summary>Get state of a payment using objects</summary>

```js
var getPaymentStateRequestBuilder = new BarionRequestBuilderFactory.BarionGetPaymentStateRequestBuilder();

var getPaymentStateOptionsWithObject = {
    POSKey      : "my_shops_pos_key_from_barion",
    PaymentId   : "payment_id_in_the_barion_system"
};

barion.getPaymentState(getPaymentStateOptionsWithObject, function(err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

<details>
<summary>Get state of a payment using request builders</summary>

```js
var getPaymentStateRequestBuilder = new BarionRequestBuilderFactory.BarionGetPaymentStateRequestBuilder();

var getPaymentStateOptionsWithBuilder = getPaymentStateRequestBuilder
    .setPOSKey('my_shops_pos_key_from_barion')
    .setPaymentId('payment_id_in_the_barion_system')
    .build();

barion.getPaymentState(getPaymentStateOptionsWithBuilder, function(err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

### Refund a payment

<details>
<summary>Refund a payment using objects</summary>

```js
var paymentRefundRequestBuilder = new BarionRequestBuilderFactory.BarionPaymentRefundRequestBuilder();

var paymentRefundOptionsWithObject = {
    POSKey      : "my_shops_pos_key_from_barion",
    PaymentId   : "payment_id_in_the_barion_system",
    TransactionsToRefund : [ 
        {
            TransactionId   : "barion_transaction_id",
            POSTransactionId : "test_payment_id_from_shop",
            AmountToRefund : 10
        }
    ]    
};

barion.refund(paymentRefundOptionsWithObject, function(err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

<details>
<summary>Refund a payment using request builders</summary>

```js
var paymentRefundRequestBuilder = new BarionRequestBuilderFactory.BarionPaymentRefundRequestBuilder();
var transactionToRefundBuilder = new paymentRefundRequestBuilder.BarionTransactionToRefundBuilder();

var refundTransaction = transactionToRefundBuilder
    .setTransactionId('barion_transaction_id')
    .setPOSTransactionId('test_payment_id_from_shop')
    .setAmountToRefund(10)
    .build();

var paymentRefundOptionsWithBuilder = paymentRefundRequestBuilder
    .setPOSKey('my_shops_pos_key_from_barion')
    .setPaymentId('payment_id_in_the_barion_system')
    .addTransactionToRefund(refundTransaction)
    .build();

barion.refund(paymentRefundOptionsWithBuilder, function(err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

### Request withdrawal

<details>
<summary>Request withdrawal using objects</summary>

```js
var withdrawBankTransferRequestBuilder = new BarionRequestBuilderFactory.BarionWithdrawBankTransferRequestBuilder();

var withdrawBankTransferOptionsWithObject = {
    UserName : "my_barion_user@gmail.com",
    Password : "myweakpassword",
    Currency : "HUF",
    Amount : 1000,
    RecipientName : "Partner Name",
    Comment : "Withdrawal",
    BankAccount : {
        Country : "HUN",
        Format : 1,
        AccountNumber : "bank_account_number"
    }
};

barion.withdraw(withdrawBankTransferOptionsWithObject, function(err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

<details>
<summary>Request withdrawal using request builders</summary>

```js
var withdrawBankTransferRequestBuilder = new BarionRequestBuilderFactory.BarionWithdrawBankTransferRequestBuilder();
var bankAddressBuilder = new withdrawBankTransferRequestBuilder.BarionBankAccountBuilder();

var bankAccount = bankAddressBuilder
    .setCountry("HUN")
    .setFormat(1)
    .setAccountNumber("bank_account_number")
    .build();

var withdrawBankTransferOptionsWithBuilder = withdrawBankTransferRequestBuilder
    .setUsername("my_barion_user@gmail.com")
    .setPassword("myweakpassword")
    .setCurrency("HUF")
    .setAmount(1000)
    .setRecipientName("Partner Name")
    .setComment("Withdrawal")
    .setBankAccount(bankAccount)
    .build();

barion.withdraw(withdrawBankTransferOptionsWithBuilder, function(err, data) {
    if (!err) {
        handleData(data);
    } else {
        handleError(err);
    }
});
```

</details>

## API Reference

You can find the documentation [here](http://plezervikt.org/docs/barion-nodejs)

## Support

You can find support in the [Barion Developers Facebook group](https://www.facebook.com/groups/bariondevelopershungary/).

## License

The library is licensed under Apache 2.0 license.
