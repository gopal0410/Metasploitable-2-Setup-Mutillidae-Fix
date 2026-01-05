# Metasploitable 2 Setup & Mutillidae II Database Fix

## Description
In this project, Metasploitable 2 was installed and configured in a virtual machine environment. After the successful setup of the virtual machine, the Mutillidae II web application was accessed through the browser.

During testing, Mutillidae II displayed a database-related error indicating that the required database or tables were missing or misconfigured. The issue was identified by reviewing the application’s database configuration and verifying the database setup. The database error was fixed, and Mutillidae II was confirmed to be running correctly without errors in the browser.

Screenshots were taken to verify the successful setup of Metasploitable 2 and the resolution of the Mutillidae II database issue.

## Metasploitable 2 Setup
Metasploitable is an intentionally vulnerable Linux virtual machine. This VM can be used to conduct security training, test security tools, and practice common penetration testing techniques.

The default login and password is msfadmin:msfadmin.

Metasploitable 2 was downloaded from [this](https://sourceforge.net/projects/metasploitable/) link here.

After downloading Metasploitable 2 and run it on VMWare. The given link has files for VMWare.

![metasploitable-2-launch](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/metasploitable-2-setup-1.png)

Changing the network adapted to bridged such that I can access its website from my windows.
![network-adapter-bridged](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/network-adapter-bridged.png)

![metasploitable-2-setup-complete](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/metasploitable-2-setup-complete.png)

Now, we can see that Metasploitable 2 has been successfully setup.

## Adding user to the VM
We can add a user in any linux machine using the following command
`sudo adduser <username>`
thus, I added the user with username as `gopal`.

![useradd](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/adduser.png)

## Checking the VM's IP address

Before we begin debugging the issue. We have to check if the website is running as intended. So we first check its IP Address so that we can view the website in our windows. This is where changing the network adapter's setting to bridged from host-only comes in handy.

![ip-addr](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/ip-addr.png)

The ip address is then noted: `192.168.31.86`

## Viewing the Metasploitable Website

Using the IP address, we tried accessing the website which worked correctly as shown in the image.

![metasploitable-2-web-open](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/metasploitable-2-web-open.png)

## Mutillidae Database Error

Now, since we are tasked with fixing the mutillidae database error, we first enter its login section and try logging in. But we encounter an error.
This error states that there is no such table as `accounts` in the database `metasploitable`.

![db-error](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/db-error.png)

## Checking MySQL

After finding out that the issue is with the table that does not exists. Then we begin by logging into MySQL in the VM and check the databases using
`SHOW DATABASES;`

![db-confirm](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/db-confirm.png)

After getting the list of databases in MySQL, we realize that `metasploit` table does exist so we check the tables within it by using the following commands
`USE metasploit;`
`SHOW TABLES;`

![db-table-confirm](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/db-table-confirm.png)

Now, that we know that the table `accounts` actually do not exist, we have to fix it. We can either fix it by manually adding the table `accounts` with the required fields which are `username`, `password`, and `mysignature`.
But on further inspection of other databases, it is found that the database `owasp10` contains the `accounts` table which was being used in mutillidae.

![owasp10](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/owasp10.png)

## Changing the DB used in Mutillidae

Now that we know that Muntillidae was using the wrong database for its storage, we first begin with changing the directory to the directory where mutillidae exists. 
It existed in `/var/www/mutillidae/`.
This directory was shown in the error so we change our directory to that directory and then begin our change.

![document-root](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/document-root.png)

Now that we are in the directory, we search for its config file.
We can either use the `ls | less` command and look for it manually or directly use `find . -type f -iname "config.*"`
Here, `ls | less` allows us to view all the files in the directory and lets us scroll through them. Normally using `ls` does not allow us to scroll through the listed files.
And `find . -type f -iname "config.*"` directly searches for files whose names begin with `config` and can be of any type.
Here,
*   `find` : Terminal command to search for files
*   `-type`: Tells the `find` command which type we are searching for.
*   `f` : Tells that we are searching for a file not a directory or anything else.
*   `-iname` : Tells that we don't care about the case sensitivity so it should be ignored
*   `config.*` : This is the file name that is to be searched. It can be of any type which is indicated by `*`.

The search method was used by me in this case.

![config](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/config-file.png)

## Fixing Mutillidae Database Error
Now since we know that there is a database error caused due to the lack of table and that there is another database called `owasp10` which holds the actual table, we change the database that is being used in the `config.inc` file and change it from `metasploit` to `owasp10`. 

![db-change](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/db-change.png)

## Checking Mutillidae After Changing the Database
Testing if the login section works as intended.
We first begin by registering our account.

![registered-account](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/registered-account.png)

![account-added](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/account-added.png)

Now that we have registered our account, our username and password will be present in the `accounts` table of the `owasp10` database. So, we can confirm this by logging in normally using our registered credentials.

![login](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/login.png)

![logged-in](https://github.com/gopal0410/Metasploitable-2-Setup-Mutillidae-Fix/blob/master/images/logged-in.png)

Finally, we can see that we have successfully logged into Mutillidae and fixed its issue with the database.

## Conclusion

In this project, Metasploitable 2 was successfully set up as a virtual machine and configured with a bridged network adapter to enable proper network access. The hosted web application was then tested through a browser to verify its functionality. During this process, an error was encountered while attempting to log in to the application, which was traced to a database-related issue. After identifying and fixing the error, the application was tested again and confirmed to be working as intended without any issues. This project provided practical experience in setting up a vulnerable virtual environment and troubleshooting basic web application and database configuration problems.

