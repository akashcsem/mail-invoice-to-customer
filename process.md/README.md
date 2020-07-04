<p align="center">
  <img src="https://res.cloudinary.com/dtfbvvkyp/image/upload/v1566331377/laravel-logolockup-cmyk-red.svg" width="400" height="70">
</p>

## Laravel Sending Invoice To Customer Via Mail

- First login to your gmail account
- Go to this link <a href="https://myaccount.google.com/lesssecureapps?pli=1">https://myaccount.google.com/lesssecureapps?pli=1</a>
- Allow less secure apps: ON, set on, 
<img src="allow_less_secure_on.png">

<br>

- Open .env file of your laravel project and set this info

```php

MAIL_DRIVER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your_email_address
MAIL_PASSWORD="your_email_password"
MAIL_ENCRYPTION=tls
```

<br>

- Create a route in web.php
```php

Route::get('/send-invoice-to-customer','HomeController@sendMail');
```

- Now go to HomeController and write this code
```php

namespace App\Http\Controllers;
use App\Mail\CustomerInvoiceMail;

class HomeController extends Controller
{
    public function sendMail()
    {
        try {
            $customer_email = 'customer1@gmail.com';
            $data = Sale::find(1);   // you should fetch your data
            Mail::to($customer_email)->send(new CustomerInvoiceMail($data));
            return 'Mail send success to customer';
        } catch (\Exception $ex) {
            return $ex->getMessage();
        }
    }
}
```

- Write this command from your terminal from project directory php ```artisan make:mail CustomerInvoiceMail``` 
- If you don't able to create this file, you can create CustomerInvoiceMail.php on Project directory > app > Mail

<img src="customer_mail_file-directory.png">
- After creating CustomerInvoiceMail.php file, just replace this code, or manually change the code

```php

<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;
use Illuminate\Contracts\Queue\ShouldQueue;

class CustomerInvoiceMail extends Mailable
{
    use Queueable, SerializesModels;

    private $data;
    /**
     * Create a new message instance.
     *
     * @return void
     */
    public function __construct($data)
    {
        $this->data = $data;
    }

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('mail.customer-invoice-template', ['data' => $this->data])
            ->subject('Mail Subject');
    }
}
 ```

- Now create a blade file on project directory > resources > views > mail

<img src="template_directory.png">

- And then write this code on ``` customer-invoice-template.blade.php ```  for mail murcup 

```html 
<!DOCTYPE html>

<html lang="en">
    <head>
        <title>Invoice Report</title>
        <style type="text/css">
            table {
                border-collapse: collapse;
            }

            table, th, td {
                border: 1px solid black;
            }
            th, td {
                padding: 5px;
            }
        </style>
    </head>

    <body>
        <div class="container" style="width: 96%; margin-left: 2%;">
            <h2 style="text-align: center">Invoice Report</h2>

            <table style="width: 100%">
                <thead>
                    <tr>
                        <th style="text-align: left">Sl</th>
                        <th style="text-align: left">Product Name</th>
                        <th style="text-align: right">Unit Price</th>
                        <th style="text-align: center">Quantity</th>
                        <th style="text-align: right">Total</th>
                    </tr>
                </thead>

                <tbody>
                    <tr>
                        <td>1</td>
                        <td>Apple</td>
                        <td style="text-align: right">150.00</td>
                        <td style="text-align: center">2</td>
                        <td style="text-align: right">300.00</td>
                    </tr>
                    <tr>
                        <td>1</td>
                        <td>Banana</td>
                        <td style="text-align: right">120.00</td>
                        <td style="text-align: center">3</td>
                        <td style="text-align: right">360.00</td>
                    </tr>
                </tbody>

                <tfoot>
                    <tr>
                        <th colspan="3" style="text-align: left"><strong>Total:</strong></th>
                        <th style="text-align: center">5</th>
                        <th style="text-align: right">660.00</th>
                    </tr>
                </tfoot>
            </table>
        </div>
    </body>
</html>
```

- Here all data shows statically, you can dynamic this data by $data object, that you send from HomeController
- Finally you will get this output to your customer mail, like this

<img src="invoice_output.png">
