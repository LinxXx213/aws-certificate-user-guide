# Troubleshooting managed certificate renewal<a name="troubleshooting-renewal"></a>

ACM tries to automatically renew your ACM certificates before they expire so that no action is required from you\. Consult the following topics if you have trouble with [Managed renewal for ACM certificates](managed-renewal.md)\. 

## Preparing for automatic domain validation<a name="troubleshooting-renewal-domain-validation"></a>

Before ACM can renew your certificates automatically, the following must be true:
+ Your certificate must be associated with an AWS service that is integrated with ACM\. For information about the resources that ACM supports, see [Services integrated with AWS Certificate Manager](acm-services.md)\.
+ ACM must be able to validate each domain name listed in your certificate\.

**For email\-validated certificates:**  
Configure the AWS resource that has your ACM certificate to [accept HTTPS requests from the internet](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/authorizing-access-to-an-instance.html)\. Make sure that HTTPS requests to the domain names in your certificate are routed to the resource that has your certificate\.

**For DNS\-validated certificates:**  
Make sure that your DNS configuration contains the correct CNAME records\.

## Handling failures in managed certificate renewal<a name="troubleshooting-automatic-renewal"></a>

When a certificate is 60 days away from expiration, ACM automatically attempts to renew it every hour\. If ACM is unable to renew the certificate after 15 days, you will receive an email with further instructions on how to manually fix the renewal problem\. This process differs depending on how the certificate was originally validated\.

### Managed certificate renewal for email\-validated certificates<a name="troubleshooting-renewal-email-validation-failure"></a>

ACM certificates are valid for 13 months \(395 days\)\. To be renewed, email\-validated certificates require an action by the domain owner\. ACM begins sending renewal notices 45 days before expiration, using the domain's WHOIS mailbox addresses and to five common administrator addressess\. The notifications contain a link that the domain owner can click for easy renewal\. Once all listed domains are validated, ACM issues a renewed certificate with the same ARN\.

See [Validate with Email](email-validation.md) for instructions on identifying which domains are in the `PENDING_VALIDATION` state and repeating the validation process for those domains\.

### Managed certificate renewal for DNS\-validated certificates<a name="troubleshooting-renewal-domain-validation-failure"></a>

ACM does not attempt TLS validation for DNS\-validated certificates\. If ACM fails to renew a certificate you validated with DNS validation, it is most likely due to missing or inaccurate CNAME records in your DNS configuration\. If this occurs, ACM notifies you that the certificate could not be renewed automatically\. 

**Important**  
You must insert the correct CNAME records into your DNS database\. Consult your domain registrar about how to do this\.

You can find the CNAME records for your domains by expanding your certificate and its domain entries in the ACM console\. Refer to the figures below for details\. You can also retrieve CNAME records by using the [DescribeCertificate](https://docs.aws.amazon.com/acm/latest/APIReference/API_DescribeCertificate.html) operation in the ACM API or the [describe\-certificate](https://docs.aws.amazon.com/cli/latest/reference/acm/describe-certificate.html) command in the ACM CLI\. For more information, see [DNS validationDNS validation](dns-validation.md)\.

![\[Select the target certificate from the console.\]](http://docs.aws.amazon.com/acm/latest/userguide/images/Dns-renewal-1.png)

![\[Expand the certificate window to find the certificate's CNAME information.\]](http://docs.aws.amazon.com/acm/latest/userguide/images/Dns-renewal-2.png)

If the problem persists, contact the [Support Center](https://console.aws.amazon.com/support)\.

### Understanding renewal timing<a name="troubleshooting-renewal-domain-async"></a>

[Managed renewal for ACM certificates](managed-renewal.md) is an asynchronous process\. This means that the steps don't occur in immediate succession\. After all domain names in an ACM certificate have been validated, there might be a delay before ACM obtains the new certificate\. An additional delay can occur between the time when ACM obtains the renewed certificate and the time when that certificate is deployed to the AWS resources that use it\. Therefore, changes to the certificate status can take up to several hours to appear in the console\. 