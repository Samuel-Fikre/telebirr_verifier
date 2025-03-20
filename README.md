# CBE Telebirr Receipt Verifier (Go)

A Go library for parsing and verifying Telebirr transaction receipts. This library allows you to verify Telebirr payments by checking the receipt information against expected values.

## Features

- Load receipt information from Telebirr's website using receipt number or full URL
- Parse receipt HTML and extract transaction details
- Verify receipt information against expected values
- Support for both partial and full receipt verification
- Handle special cases like bank account numbers and amounts

## Installation

```bash
go get github.com/cbe_telebirr_verifier
```

## Usage

Here's a basic example of how to use the library:

```go
package main

import (
    "fmt"
    "log"
    "github.com/cbe_telebirr_verifier/pkg/loader"
    "github.com/cbe_telebirr_verifier/pkg/parser"
    "github.com/cbe_telebirr_verifier/pkg/receipt"
)

func main() {
    // Load receipt using receipt number
    html, err := loader.LoadReceipt("YOUR_RECEIPT_NUMBER", "")
    if err != nil {
        log.Fatal(err)
    }

    // Parse receipt HTML
    parsedFields, err := parser.ParseHTML(html)
    if err != nil {
        log.Fatal(err)
    }

    // Define expected values
    expected := map[string]interface{}{
        "to": "John Doe",
        "amount": float64(1000),
    }

    // Create receipt verifier
    r := receipt.New(parsedFields, expected)

    // Verify specific fields
    verified := r.Verify(func(parsed, predefined map[string]interface{}) bool {
        return r.Equals(parsed["to"], predefined["to"]) &&
            r.Equals(parsed["amount"], predefined["amount"])
    })

    if verified {
        fmt.Println("Receipt verification successful!")
    } else {
        fmt.Println("Receipt verification failed!")
    }
}
```

## Available Fields

The following fields can be extracted from a Telebirr receipt:

- `payer_name`: Name of the person who made the payment
- `payer_phone`: Phone number of the payer
- `payer_acc_type`: Account type of the payer
- `credited_party_name`: Name of the recipient
- `credited_party_acc_no`: Account number of the recipient
- `transaction_status`: Status of the transaction
- `bank_acc_no`: Bank account number (if applicable)
- `to`: Recipient name extracted from bank account field
- `receiptNo`: Receipt number
- `date`: Transaction date
- `settled_amount`: Amount settled
- `discount_amount`: Discount amount (if any)
- `vat_amount`: VAT amount
- `total_amount`: Total amount paid
- `amount_in_word`: Amount written in words
- `payment_mode`: Mode of payment
- `payment_reason`: Reason for payment
- `payment_channel`: Payment channel used

## Verification Methods

The library provides three methods for verifying receipt information:

1. `Verify(callback)`: Custom verification using a callback function
2. `VerifyAll(doNotCompare)`: Verify all fields except those in doNotCompare
3. `VerifyOnly(fieldNames)`: Verify only specific fields

## Error Handling

The library provides detailed error messages for:
- Failed receipt loading
- HTML parsing errors
- Invalid field types
- Missing required fields

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Disclaimer

This is an unofficial library and is not affiliated with Telebirr or Ethio Telecom. Use at your own risk.