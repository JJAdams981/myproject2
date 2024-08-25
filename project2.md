Here's a step-by-step guide on setting up multiple static websites on a single server using Nginx virtual hosts on AWS:

Prerequisites:

- An AWS EC2 instance (e.g., Ubuntu) with Nginx installed
- Multiple static websites (toot)
- A registered domain name (jjadams.store) with DNS records managed by Route 53

Key Concepts Covered
AWS (EC2 and Route 53)
EC2
Linux(Ubuntu)
Nginx
DNS
Subdomains
SSL (certbot)
OpenSSL command

Checklist
 Task 1: Spin up a Ubuntu server & assign an elastic IP to it.
 Task 2: SSH into the server and install and configure Nignx on a server.
 Task 3: Create two website directories with two different website templates.
 Task 4: Create two subdomains
 Task 5: Add the IP of the server as A record to the two subdomains.
 Task 6: Configure the Virtual host to point two subdomains to two different website directories.
 Task 7: Validate the setup by accessing the subdomains.
 Task 8: Create a certbot SSL certificate for the root Domain.
 Task 9: Configure certbot on Nginx for the two websites.
 Task 10: Validate the subdomain websites’ SSL using OpenSSL utility.

Documentation
*****:Spin up a Ubuntu server & assign an elastic IP to it

Step 1: Launch an Ubuntu Server

1. Log in to the AWS Management Console.
2. Navigate to the EC2 dashboard.
3. Click "Launch Instance" in the top right corner.
4. Select "Ubuntu Server" as the Amazon Machine Image (AMI).
5. Choose the instance type (e.g., t2.micro) and click "Next: Configure Instance Details".
6. Configure the instance details as needed (e.g., VPC, subnet, security group).
7. Click "Next: Add Storage" and then "Next: Add Tags".
8. Add any desired tags (e.g., Name, Environment) and click "Next: Configure Security Group".
9. Select an existing security group or create a new one.
10. Click "Review and Launch" and then "Launch".

Step 2: Create an Elastic IP

1. Navigate to the VPC dashboard.
2. Click "Elastic IPs" in the top left corner.
3. Click "Allocate Elastic IP".
4. Select "IPv4" as the IP address type.
5. Click "Allocate".

Step 3: Associate the Elastic IP with your instance

1. Navigate to the EC2 dashboard.
2. Select your instance.
3. Click "Actions" and then "Networking" > "Manage IP addresses".
4. Click "Associate Elastic IP".
5. Select the Elastic IP you created.
6. Click "Associate".

Step 4: Verify the Elastic IP

1. Navigate to the EC2 dashboard.
2. Select your instance.
3. Verify that the Elastic IP is listed under "Public IP".

That's it! You now have an Ubuntu server running with an associated Elastic IP.

Note: Make sure to replace the example values with your actual values. Also, ensure that your security group allows incoming traffic on the desired ports (e.g., port 80 for HTTP).

Task 2: SSH into the server and install and configure Nignx on a server.


****SSH into the server and install Nginx

Step 1: SSH into the server

1. Open a terminal on your local machine.
2. Use the SSH command to connect to your server:

ssh -i "CUBE2.pem" ubuntu@ec2-98-80-28-181.compute-1.amazonaws.com

Replace /path/to/your/private/key.pem with the path to your private key file and your-elastic-ip with your Elastic IP address.

Step 2: Update the package list

1. Once connected, update the package list:

sudo apt update

Step 3: Install Nginx

1. Install Nginx:

sudo apt install nginx

Step 4: Start and enable Nginx

1. Start Nginx:

sudo systemctl start nginx

1. Enable Nginx to start automatically on boot:

sudo systemctl enable nginx

****Create two website directories with two different website templates.

Download your website template from your preferred website by navigating to the website, locating the template you want.

Right click and select Inspect from the drop down menu.

Click on the Network tab and then click Download button.

Right click on the website name, select Copy and click on Copy link address.


https://www.tooplate.com/zip-templates/2098_health.zip
https://www.tooplate.com/zip-templates/2132_clean_work.zip

*******To install the unzip tool, run the following command: sudo apt install unzip.

Execute the command to download and unzip your website files sudo curl -o /var/www/html/2132_clean_work.zip.zip https://www.tooplate.com/zip-templates/2098_health.zip && sudo unzip -d /var/www/html/ /var/www/html/2098_health.zip && sudo rm -f /var/www/html/2098_health.zip.

Here's an explanation of the command:

The command sudo curl -o /var/www/html/2098_health.zip https://www.tooplate.com/zip-templates/2098_health.zip && sudo unzip -d /var/www/html/ /var/www/html/2098_health.zip && sudo rm -f /var/www/html/2098_health.zip performs a series of actions to download, unzip, and clean up a website template file. Here’s a breakdown of each part of the command:

sudo curl -o /var/www/html/2098_health.zip https://www.tooplate.com/zip-templates/2098_health.zip:

sudo: Runs the command with superuser (root) privileges.
curl -o /var/www/html/2098_health.zip: Downloads the file from the specified URL (https://www.tooplate.com/zip-templates/2098_health.zip) and saves it as 2098_health.zip in the /var/www/html directory.
&&: Logical AND operator, which ensures that the next command runs only if the previous command succeeds.

sudo unzip -d /var/www/html/ /var/www/html/2098_health.zip:

sudo: Runs the command with superuser (root) privileges.
unzip -d /var/www/html/: Extracts the contents of the zip file into the /var/www/html/ directory.
/var/www/html/2098_health.zip: Specifies the path to the zip file to be unzipped.
&&: Logical AND operator, which ensures that the next command runs only if the previous command succeeds.

sudo rm -f /var/www/html/2098_health.zip:

sudo: Runs the command with superuser (root) privileges.
rm -f: Removes (deletes) the specified file forcefully (without prompting for confirmation).
/var/www/html/2098_health.zip: Specifies the path to the zip file to be deleted.
In summary, this command downloads the website template file, extracts its contents to the web server directory, and then deletes the downloaded zip file to clean up the directory.

*****Download the 2nd website template by running the following command:
sudo curl -o /var/www/html/2132_clean_work.zip https://www.tooplate.com/zip-templates/2132_clean_work.zip && sudo unzip -d /var/www/html/ /var/www/html/2132_clean_work.zip && sudo rm -f /var/www/html/2132_clean_work.zip

Note

Replace the placeholders in the code with your own website URL. For example, substitute https://www.tooplate.com/zip-templates/2132_clean_work.zip with your chosen URL, and change 2132_clean_work.zip accordingly.

*******To set up your website's configuration, start by creating a new file in the Nginx sites-available directory. 
1. Create a new configuration file for your website:

sudo nano /etc/nginx/sites-available/cleaning

Replace your-website with your website name.

1. Add the following configuration:

server {
    listen 80;
    server_name cleaning.jjadams.store wwwcleaning.jjadams.store;

    root /var/www/html/2132_clean_work
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

Replace your-website.com with your domain name and /var/www/your-website with the path to your website files.

1. Save and exit the file.

Step 6: Enable the configuration

1. Create a symbolic link to the sites-enabled directory:

sudo ln -s /etc/nginx/sites-available/your-website /etc/nginx/sites-enabled/

sudo ln -s /etc/nginx/sites-available/wedding /etc/nginx/sites-enabled/ 
sudo ln -s /etc/nginx/sites-available/health /etc/nginx/sites-enabled/

Step 7: Test Nginx

1. Test Nginx configuration:

sudo nginx -t

******Delete the default files in the sites-available and sites-enabled directories by executing the following commands:

sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default

1. Restart Nginx:

sudo systemctl restart nginx

That's it! You should now have Nginx installed and configured on your server.

Note: Make sure to replace the example values with your actual values. Also, ensure that your security group allows incoming traffic on port 80 (HTTP).

Step 1: Create a directory for each website

Create separate directories for each website in the /var/www/ directory. For example:

bash
sudo mkdir /var/www/website1
sudo mkdir /var/www/website2

Step 2: Create an index.html file for each website

Create an index.html file in each website directory. This will serve as the default page for each website.

Step 3: Configure Nginx virtual hosts

Create a new file in the /etc/nginx/sites-available/ directory for each website. For example:

bash
sudo nano /etc/nginx/sites-available/website1
sudo nano /etc/nginx/sites-available/website2

In each file, add the following configuration:

nginx
server {
    listen 80;
    server_name website1.example.com;

    root /var/www/website1;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

Replace website1.example.com with your domain name and /var/www/website1 with the corresponding directory.

Step 4: Enable the virtual hosts

Create symbolic links to the sites-enabled directory:

bash
sudo ln -s /etc/nginx/sites-available/website1 /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/website2 /etc/nginx/sites-enabled/

Step 5: Configure Route 53

Create a new record set in Route 53 for each website:

- Go to the Route 53 dashboard
- Select your hosted zone (e.g., example.com)
- Click "Create record set"
- Enter the subdomain (e.g., website1) and select "A - IPv4 address"
- Enter the IP address of your EC2 instance

Repeat this process for each website.

Step 6: Restart Nginx

Restart Nginx to apply the changes:

bash
sudo service nginx restart

Step 7: Test the websites

Access each website by visiting the corresponding domain name in your web browser.

That's it! You should now have multiple static websites running on a single server using Nginx virtual hosts on AWS with Route 53.

Note: Make sure to replace the example domain names and directory names with your actual values. Also, ensure that your DNS records are pointing to your EC2 instance.

****Go to your domain name in a web browser to verify that your website is accessible.


********Install certbot and Request For an SSL/TLS Certificate


Install certbot by executing the following commands: 

sudo apt update

sudo apt install python3-certbot-nginx 

sudo certbot --nginx



[NOTE] In this case, SSL certificates were only created for cleaning.jjadams.store and health.jjadams.store. So, when prompted, I entered the numbers 1 and 3 (corresponding to those two domains) to select them for certificate generation. If we had also created records for www.cleaning.jjadams.store and www.health.jjadams.store, I could have simply pressed Enter to accept the default selection (all available records). If you try to create a certificate without having created an A record first, you will receive an error message.


*****Verify the website's SSL using the OpenSSL utility with the command: openssl s_client -connect cleaning.jjadams.store:443

Visit https://cleaning.jjadams.store  to view your websites.