# How to access localhost from mobile device

In my project, I need to test the camera function of mobile device with our website. So I search and found the solution, how I can connect my smartphone (or tablet) to my laptop and access the website on localhost. This article will show you how to achieve this. The method works with both Android and iOS devices.

## Connect to your Wi-Fi

Both your laptop and mobile device have to connect to a same Wi-Fi network. I didn't test with a internal network like in a company, but in theory it should work too.

## Host your website in your laptop

You can host your website on `localhost` or IP address `127.0.0.1`, but not on a custom domain name, which only your laptop can understand, e.g., `mysite.local`.

## Find out private IP address of your laptop Wi-Fi adapter

Open a command prompt window and run the command `ipconfig`. The IP address should be displayed under IPv4 Address.

## Open the website in mobile device

For example:

- Your website is hosted and can be accessed under `http://localhost:4200` in your laptop
- The private IP address of Wi-Fi adapter is `123.456.78.99`

To access the website from your mobile device, let open this url `http://123.456.78.99:4200` in mobile browser.
