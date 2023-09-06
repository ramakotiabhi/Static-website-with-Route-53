#  Static website with CloudFront and Route53

Here, Suppose that you want to host a static website on Amazon S3. You've registered a domain with Amazon Route 53 (for example, example.com), and you want requests for http://www.example.com and http://example.com to be served from your Amazon S3 content. 
**Note**
Amazon S3 website endpoints do not support HTTPS or access points. If you want to use HTTPS, you can use Amazon CloudFront to serve a static website hosted on Amazon S3.

You can use an AWS CloudFormation template to automate your static website setup. The AWS CloudFormation template sets up the components that you need to host a secure static website so that you can focus more on your website’s content and less on configuring components.
The AWS CloudFormation template includes the following components:
* Amazon S3 – Creates an Amazon S3 bucket to host your static website.
* CloudFront – Creates a CloudFront distribution to speed up your static website.
* Lambda@Edge – Uses Lambda@Edge to add security headers to every server response. Security headers are a group of headers in the web server response that tell web browsers to take extra security precautions.

**Amazon Route 53** – You use Route 53 to register domains and to define where you want to route internet traffic for your domain. The example shows how to create Route 53 alias records that route traffic for your domain (example.com) and subdomain (www.example.com) to an Amazon S3 bucket that contains an HTML file.

**Amazon S3** – You use Amazon S3 to create buckets, upload a sample website page, configure permissions so that everyone can see the content, and then configure the buckets for website hosting.

 ## Step 1: Register a custom domain with Route 53

 If you don't already have a registered domain name, such as example.com, register one with Route 53. After you register your domain name, you can create and configure your Amazon S3 buckets for website hosting.

 ## Step 2: Create two buckets

 To support requests from both the root domain and subdomain, you create two buckets.

* **Domain bucket** – example.com
* **Subdomain bucket** – www.example.com

These bucket names must match your domain name exactly. In this example, the domain name is example.com. You host your content out of the root domain bucket (example.com). You create a redirect request for the subdomain bucket (www.example.com). If someone enters www.example.com in their browser, they are redirected to example.com and see the content that is hosted in the Amazon S3 bucket with that name.

**To create your buckets for website hosting**

1. open the Amazon S3 .
2. Create your root domain bucket:
a. Choose **Create bucket**.
b. Enter the **Bucket name** (for example, example.com).
c. Choose the Region where you want to create the bucket.
Choose a Region that is geographically close to you to minimize latency and costs, or to address regulatory requirements. The Region that you choose determines your Amazon S3 website endpoint.
d. To accept the default settings and create the bucket, choose **Create**.
3. Create your subdomain bucket:
a. Choose Create bucket.
b. Enter the Bucket name (for example, www.example.com).
c. Choose the Region where you want to create the bucket.
Choose a Region that is geographically close to you to minimize latency and costs, or to address regulatory requirements. The Region that you choose determines your Amazon S3 website endpoint. For more information, see Website endpoints.
d. To accept the default settings and create the bucket, choose Create.

## Step 3: Configure your root domain bucket for website hosting

In this step, you configure your root domain bucket (example.com) as a website. This bucket will contain your website content. 

**To enable static website hosting**
1. open the Amazon S3
2. In the **Buckets** list, choose the name of the bucket that you want to enable static website hosting for.
3. Choose **Properties**.
4. Under **Static website hosting**, choose **Edit**.
5. Choose **Use this bucket to host a website**.
6. Under **Static website hosting, choose Enable**.
7. In **Index document**, enter the file name of the index document, typically index.html.
The index document name is case sensitive and must exactly match the file name of the HTML index document that you plan to upload to your S3 bucket. When you configure a bucket for website hosting, you must specify an index document. Amazon S3 returns this index document when requests are made to the root domain or any of the subfolders. 
8. To provide your own custom error document for 4XX class errors, in **Error document**, enter the custom error document file name.
The error document name is case sensitive and must exactly match the file name of the HTML error document that you plan to upload to your S3 bucket. If you don't specify a custom error document and an error occurs, Amazon S3 returns a default HTML error document.
9. (Optional) If you want to specify advanced redirection rules, in **Redirection rules**, enter JSON to describe the rules.
For example, you can conditionally route requests according to specific object key names or prefixes in the request.
10. Choose **Save changes**.
Amazon S3 enables static website hosting for your bucket. At the bottom of the page, under **Static website hosting**, you see the website endpoint for your bucket.
11. Under **Static website hosting**, note the **Endpoint**.
The **Endpoint** is the Amazon S3 website endpoint for your bucket. After you finish configuring your bucket as a static website, you can use this endpoint to test your website.

After you **edit block public access settings** and **add a bucket policy** that allows public read access, you can use the website endpoint to access your website.

In the next step, you configure your subdomain (www.example.com) to redirect requests to your domain (example.com).

## Step 4: Configure your subdomain bucket for website redirect

After you configure your root domain bucket for website hosting, you can configure your subdomain bucket to redirect all requests to the domain. In this example, all requests for www.example.com are redirected to example.com.

**To configure a redirect request**

1. On the Amazon S3 console, in the **Buckets** list, choose your subdomain bucket name (www.example.com in this example).
2. Choose **Properties**.
3. Under **Static website hosting**, choose **Edit**.
4. Choose **Redirect requests for an object**.
5. In the **Target bucket** box, enter your root domain, for example, example.com.
6. For **Protocol**, choose **http**.
7. Choose **Save changes**.

## Step 5: Configure logging for website traffic

If you want to track the number of visitors accessing your website, you can optionally enable logging for your root domain bucket. 
 If you plan to use Amazon CloudFront to speed up your website, you can also use CloudFront logging.

**To enable server access logging for your root domain bucket**

1. Open the Amazon S3 console.
2.In the same Region where you created the bucket that is configured as a static website, create a bucket for logging, for example logs.example.com.
3. Create a folder for the server access logging log files (for example, logs).
4. (Optional) If you want to use CloudFront to improve your website performance, create a folder for the CloudFront log files (for example, cdn).
5. In the **Buckets** list, choose your root domain bucket.
6. Choose **Properties**.
7. Under **Server access logging**, choose **Edit**.
8. Choose **Enable**.
9. Under the **Target bucket**, choose the bucket and folder destination for the server access logs:
    * Browse to the folder and bucket location:
        a. Choose **Browse S3**.
        b. Choose the bucket name, and then choose the logs folder.
        c. Choose **Choose path**.
    * Enter the S3 bucket path, for example, s3://logs.example.com/logs/.
10. Choose **Save changes**.
In your log bucket, you can now access your logs. Amazon S3 writes website access logs to your log bucket every 2 hours.

## Step 6: Upload index and website content

In this step, you upload your index document and optional website content to your root domain bucket.

When you enable static website hosting for your bucket, you enter the name of the index document (for example, index.html). After you enable static website hosting for the bucket, you upload an HTML file with this index document name to your bucket.

**To configure the index document**
1. Create an index.html file.
If you don't have an index.html file, you can use the following HTML to create one:

<html xmlns="http://www.w3.org/1999/xhtml" >
<head>
    <title>My Website Home Page</title>
</head>
<body>
  <h1>Welcome to my website</h1>
  <p>Now hosted on Amazon S3!</p>
</body>
</html>

2. Save the index file locally.
The index document file name must exactly match the index document name that you enter in the **Static website hosting** dialog box. The index document name is case sensitive. For example, if you enter index.html for the **Index document** name in the **Static website hosting** dialog box, your index document file name must also be index.html and not Index.html.
3. open the Amazon S3 console.
4. In the **Buckets** list, choose the name of the bucket that you want to use to host a static website.
5. Enable static website hosting for your bucket, and enter the exact name of your index document (for example, index.html).
6. To upload the index document to your bucket, do one of the following:
   * Drag and drop the index file into the console bucket listing.
   * Choose Upload, and follow the prompts to choose and upload the index file.
7. (Optional) Upload other website content to your bucket.

## Step 7: Upload an error document

When you enable static website hosting for your bucket, you enter the name of the error document (for example, 404.html). After you enable static website hosting for the bucket, you upload an HTML file with this error document name to your bucket.

**To configure an error document**
1. Create an error document, for example 404.html.
Save the error document file locally.
The error document name is case sensitive and must exactly match the name that you enter when you enable static website hosting. For example, if you enter 404.html for the** Error document** name in the **Static website hosting** dialog box, your error document file name must also be 404.html.
3. open the Amazon S3 console.
4. In the **Buckets** list, choose the name of the bucket that you want to use to host a static website.
5. Enable static website hosting for your bucket, and enter the exact name of your error document (for example, 404.html). 
6. To upload the error document to your bucket, do one of the following:
   * Drag and drop the error document file into the console bucket listing.
   * Choose Upload, and follow the prompts to choose and upload the index file.

## Step 8: Edit S3 Block Public Access settings

You edit block public access settings for the domain bucket (example.com) to allow public access.

By default, Amazon S3 blocks public access to your account and buckets. If you want to use a bucket to host a static website, you can use these steps to edit your block public access settings.

1. Open the Amazon S3 console.
2. Choose the name of the bucket that you have configured as a static website.
3. Choose **Permissions**.
4. Under **Block public access (bucket settings)**, choose **Edit**.
5. Clear **Block all public access**, and choose **Save changes**.

Amazon S3 turns off Block Public Access settings for your bucket. To create a public, static website, you might also have to **edit the Block Public Access settings** for your account before adding a bucket policy. If account settings for Block Public Access are currently turned on, you see a note under Block public access (bucket settings).

## Step 9: Attach a bucket policy

 you attach a bucket policy to the domain bucket (example.com) to allow public read access. You replace the Bucket-Name in the example bucket policy with the name of your domain bucket, for example example.com.
After you edit S3 Block Public Access settings, you can add a bucket policy to grant public read access to your bucket. When you grant public read access, anyone on the internet can access your bucket.

1. Under **Buckets**, choose the name of your bucket.
2. Choose **Permissions**.
3. Under **Bucket Policy**, choose **Edit**.
4. To grant public read access for your website, copy the following bucket policy, and paste it in the **Bucket policy editor**.

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::Bucket-Name/*"
            ]
        }
    ]
}

5. Update the Resource to your bucket name.
In the preceding example bucket policy, Bucket-Name is a placeholder for the bucket name. To use this bucket policy with your own bucket, you must update this name to match your bucket name.
6. Choose **Save changes**.
A message appears indicating that the bucket policy has been successfully added.
If you see an error that says Policy has invalid resource, confirm that the bucket name in the bucket policy matches your bucket name.

If you get an error message and cannot save the bucket policy, check your account and bucket Block Public Access settings to confirm that you allow public access to the bucket.

## Step 10: Test your domain endpoint

1. Under **Buckets**, choose the name of your bucket.
2. Choose **Properties**.
3. At the bottom of the page, under **Static website hosting**, choose your **Bucket website endpoint**.
Your index document opens in a separate browser window.

## Step 11: Add alias records for your domain and subdomain

In this step, you create the alias records that you add to the hosted zone for your domain maps example.com and www.example.com. Instead of using IP addresses, the alias records use the Amazon S3 website endpoints. Amazon Route 53 maintains a mapping between the alias records and the IP addresses where the Amazon S3 buckets reside. You create two alias records, one for your root domain and one for your subdomain.

**Add an alias record for your root domain and subdomain**

To add an alias record for your root domain (example.com)

1. Open the Route 53 console.
2. Choose **Hosted zones**.
3. In the list of hosted zones, choose the name of the hosted zone that matches your domain name.
4. Choose **Create record**.
5. Choose **Switch to wizard**.
6. Choose **Simple routing**, and choose **Next**.
7. Choose **Define simple record**.
8. In **Record name**, accept the default value, which is the name of your hosted zone and your domain.
9. In** Value/Route traffic to**, choose **Alias to S3 website endpoint**.
10. Choose the Region.
11. Choose the S3 bucket.
The bucket name should match the name that appears in the **Name** box. In the **Choose S3 bucket** list, the bucket name appears with the Amazon S3 website endpoint for the Region where the bucket was created, for example, s3-website-us-west-1.amazonaws.com (example.com).
**Choose S3 bucket** lists a bucket if:
    * You configured the bucket as a static website.
    * The bucket name is the same as the name of the record that you're creating.
    * The current AWS account created the bucket.
If your bucket does not appear in the **Choose S3 bucket** list, enter the Amazon S3 website endpoint for the Region where the bucket was created, for example, s3-website-us-west-2.amazonaws.com.
12. In **Record type**, choose **A ‐ Routes traffic to an IPv4 address and some AWS resources.**
13. For **Evaluate target health**, choose** No**.
14. Choose **Define simple record**.

**To add an alias record for your subdomain (www.example.com)**

1. Under **Configure records**, choose **Define simple record**.
2. In **Record name** for your subdomain, type www.
3. In **Value/Route traffic to**, choose **Alias to S3 website endpoint**.
4. Choose the Region.
5. Choose the S3 bucket, for example, s3-website-us-west-2.amazonaws.com (www.example.com).
If your bucket does not appear in the Choose S3 bucket list, enter the Amazon S3 website endpoint for the Region where the bucket was created, for example, s3-website-us-west-2.amazonaws.com.
6. In **Record type**, choose** A ‐ Routes traffic to an IPv4 address and some AWS resources.**
For **Evaluate target health**, choose **No**.
Choose **Define simple record**.
On the **Configure records** page, choose **Create records**.

## Step 12: Test the website

Verify that the website and the redirect work correctly. In your browser, enter your URLs.

* **Domain** (http://example.com) – Displays the index document in the example.com bucket.
* **Subdomain** (http://www.example.com) – Redirects your request to http://example.com. You see the index document in the example.com bucket.

If your website or redirect links don't work, you can try the following:
* **Clear cache** – Clear the cache of your web browser.
* **Check name servers** – If your web page and redirect links don't work after you've cleared your cache, you can compare the name servers for your domain and the name servers for your hosted zone. If the name servers don't match, you might need to update your domain name servers to match those listed under your hosted zone.

After you've successfully tested your root domain and subdomain, you can set up an **Amazon CloudFront** distribution to improve the performance of your website and provide logs that you can use to review website traffic.

# Speeding up your website with Amazon CloudFront

You can use Amazon CloudFront to improve the performance of your Amazon S3 website. CloudFront makes your website files (such as HTML, images, and video) available from data centers around the world (known as edge locations). When a visitor requests a file from your website, CloudFront automatically redirects the request to a copy of the file at the nearest edge location. This results in faster download times than if the visitor had requested the content from a data center that is located farther away.

CloudFront caches content at edge locations for a period of time that you specify. If a visitor requests content that has been cached for longer than the expiration date, CloudFront checks the origin server to see if a newer version of the content is available. If a newer version is available, CloudFront copies the new version to the edge location. Changes that you make to the original content are replicated to edge locations as visitors request the content.

## Step 1: Create a CloudFront distribution

First, you create a CloudFront distribution. This makes your website available from data centers around the world.

**To create a distribution with an Amazon S3 origin**
1. Open the CloudFront console.
2. Choose **Create Distribution**.
3. On the **Create Distribution** page, in the **Origin Settings** section, for **Origin Domain** **Name**, enter the Amazon S3 website endpoint for your bucket—for example, example.com.s3-website.us-west-1.amazonaws.com.
CloudFront fills in the **Origin ID** for you.
4. For **Default Cache Behavior Settings**, keep the values set to the defaults.
With the default settings for **Viewer Protocol Policy**, you can use HTTPS for your static website.
5. For **Distribution Settings**, do the following:
    a. Leave Price Class set to **Use All Edge Locations (Best Performance).**
    b. Set **Alternate Domain Names (CNAMEs)** to the root domain and www subdomain. In this tutorial, these are example.com and www.example.com.
    c. For **SSL Certificate**, choose **Custom SSL Certificate (example.com)**, and choose the custom certificate that covers the domain and subdomain names.
    d. In **Default Root Object**, enter the name of your index document, for example, index.html.
    If the URL used to access the distribution doesn't contain a file name, the CloudFront distribution returns the index document. The **Default Root Object** should exactly match the name of the index document for your static website.
    e. Set **Logging** to **On**.
    f. For **Bucket for Logs**, choose the logging bucket that you created.
    g. If you want to store the logs that are generated by traffic to the CloudFront distribution in a folder, in **Log Prefix**, enter the folder name.
    h. Keep all other settings at their default values.
6. Choose **Create Distribution**.
7. To see the status of the distribution, find the distribution in the console and check the ** Status** column.
A status of InProgress indicates that the distribution is not yet fully deployed.
After your distribution is deployed, you can reference your content with the new CloudFront domain name.
8. Record the value of **Domain Name** shown in the CloudFront console, for example, dj4p1rv6mvubz.cloudfront.net.
9. To verify that your CloudFront distribution is working, enter the domain name of the distribution in a web browser.

If your website is visible, the CloudFront distribution works. If your website has a custom domain registered with Amazon Route 53, you will need the CloudFront domain name to update the record set in the next step.

## Step 2: Update the record sets for your domain and subdomain

Now that you have successfully created a CloudFront distribution, update the alias record in Route 53 to point to the new CloudFront distribution.

**To update the alias record to point to a CloudFront distribution**
1. Open the Route 53 console.
2. In the left navigation, choose **Hosted zones**.
3. On the **Hosted Zones** page, choose the hosted zone that you created for your subdomain, for example, www.example.com.
4. Under **Records**, select the A record that you created for your subdomain.
5. Under **Record details**, choose **Edit record**.
6. Under **Route traffic to**, choose** Alias to CloudFront distribution.**
7. Under **Choose distribution**, choose the CloudFront distribution.
8. Choose **Save**.
9. To redirect the A record for the root domain to the CloudFront distribution, repeat this procedure for the root domain, for example, example.com.
The update to the record sets takes effect within 2–48 hours.
10. To see whether the new A records have taken effect, in a web browser, enter your subdomain URL, for example, http://www.example.com.
If the browser no longer redirects you to the root domain (for example, http://example.com), the new A records are in place. When the new A record has taken effect, traffic routed by the new A record to the CloudFront distribution is not redirected to the root domain. Any visitors who reference the site by using http://example.com or http://www.example.com are redirected to the nearest CloudFront edge location, where they benefit from faster download times.

## (Optional) Step 3: Check the log files

The access logs tell you how many people are visiting the website. They also contain valuable business data that you can analyze with other services, such as Amazon EMR.

CloudFront logs are stored in the bucket and folder that you choose when you create a CloudFront distribution and enable logging. CloudFront writes logs to your log bucket within 24 hours from when the corresponding requests are made.
**
To see the log files for your website**

1. Open the Amazon S3 console
2. Choose the name of the logging bucket for your website.
3. Choose the CloudFront logs folder.
4. Download the .gzip files written by CloudFront before opening them.

# Cleaning up

## Step 1: Delete the Amazon CloudFront distribution

Before you delete an Amazon CloudFront distribution, you must disable it. A disabled distribution is no longer functional and does not accrue charges. You can enable a disabled distribution at any time. After you delete a disabled distribution, it is no longer available.

**To disable and delete a CloudFront distribution**

1. Open the CloudFront console.
2. Select the distribution that you want to disable, and then choose **Disable**.
3. When prompted for confirmation, choose **Yes, Disable**.
4. Select the disabled distribution, and then choose **Delete**.
5. When prompted for confirmation, choose **Yes, Delete**.

## Step 2: Delete the Route 53 hosted zone
Before you delete the hosted zone, you must delete the record sets that you created. You don't need to delete the NS and SOA records; these are automatically deleted when you delete the hosted zone.

**To delete the record sets**

1. Open the Route 53 console. 
2. In the list of domain names, select your domain name, and then choose **Go to Record Sets**.
3.In the list of record sets, select the A records that you created.
The type of each record set is listed in the ** Type** column.
4. Choose **Delete Record Set.**
5. When prompted for confirmation, choose **Confirm.**

**To delete a Route 53 hosted zone**

1. Continuing from the previous procedure, choose **Back to Hosted Zones.**
2. Select your domain name, and then choose **Delete Hosted Zone.**
3. When prompted for confirmation, choose ** Confirm.**

## Step 3: Disable logging and delete your S3 bucket

Before you delete your S3 bucket, make sure that logging is disabled for the bucket. Otherwise, AWS continues to write logs to your bucket as you delete it.

**To disable logging for a bucket**

1. Open the Amazon S3 console.
2. Under **Buckets,** choose your bucket name, and then choose **Properties**.
3. From **Properties**, choose **Logging.**
4. Clear the **Enabled** check box.
5. Choose **Save**.

Now, you can delete your bucket.