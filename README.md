# Serverless Contact Us Form

Quickly deploy an endpoint to handle your `contact us` form on your static website. We don't want to have servers running just to handle our `contact us` form which rarely gets invoked. Oh, and we also need Captcha because we don't have time in our lives for spam.

You can get your reCAPTCHA secret here: https://www.google.com/recaptcha/admin#list

## Package and Deploy

```shell
aws cloudformation package --template-file stack.yml --output-template-file stack-output.yml --s3-bucket serverless-contact-us-form
```

```shell
aws cloudformation deploy --template-file stack-output.yml --stack-name contact-us --capabilities CAPABILITY_IAM --parameter-overrides "Subject=Contact Us" "ReCaptchaSecret=???" "ToEmailAddress=???"
```

## What AWS resources does this template use?

- Lambda (API Function)
- API Gateway (HTTP proxy to Lambda)
- SNS (Send us the email)
- IAM (AWS permissions & users)

## HTML Form

Check the sample standalone html page (using jquery): [./samples/contact.html](./samples/contact.html)

- Start a dummy web server or use your own one

```shell
python3 -m http.server
```

- Update the endpoint URL with your own value:

  - `var endpointPostUrl = 'https://YOUR_SERVERLESS_ID.execute-api.eu-west-1.amazonaws.com/YOUR_STAGE/YOUR_PATH';`

- Update the Google-reCAPTCHA site key with your own value:

  - `data-sitekey="YOUR_RECAPTCHA_SITE_KEY"``

- Navigate to [./samples/contact.html](./samples/contact.html)

## Debug the lambda

In AWS lambda console, you can select "configure test events", and copy/paste the content of the file `./sample.test.request.json` in the textbox, then click the 'Test' button.
The lambda should fails with an "invalid recaptcha" message: Feel free to add a valid recaptcha response (at the end of the file, in the `body` field) or to change your code temporaly for debugging.

**Note:**

- The JSON really sent by the client is in the `body` field (at the bottom of the sample file)
- This sample file contains a lot of other attributes because POST method created as an implicit `AWS_PROWY` integration type. In a regular cloudformation template (without SAM), the method would have this attribute:

```yaml
Integration:
  Type: AWS_PROXY
```
