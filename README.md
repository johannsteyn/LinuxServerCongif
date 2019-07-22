# LinuxServerCongif
Amazon Lightsail Server Set Up

1. Navaigate to Amazon Lightsail website https://lightsail.aws.amazon.com
2. Create a new AWS account and log in.
3. After sucsesfull login create new instance, select what instance you want to use. (Ubuntu 16.04 is what I used)
4. Scroll down to name, and create instance.
5. Click the box where your instance is located and scroll down past "connect using ssh" and click "Account page"
6. Navigate to SSH keys then click download.
7. Head back to the main instance page and click on your project box again.
8. Click Networking at the top of the page, and then scroll to find "add another"
9. Add port 123 and 2200.


Server Config
1. Navigate through local folders to find where the .pem file is you downloaded earlier, and now move it into your .ssh folder on your local machine.
2. We need to make the public key usable, Go back to your terminal and input $ chmod 600 ~/.ssh/yourAWSkey.pem
3. Now log in using this key into our Aamazon Lightsail server: $ ssh -i ~/.ssh/yourAWSkey.pem ubuntu@publicip
4. Amazon does not allow us to log in as root user but we can type $ sudo su - and become the root user.
5. Now as root user create user. $ sudo adduser grader
6. After going through and selecting a password and naming your new user hit Y and ENTER to confirm.
7. Now we will create a new file in the sudores.d directory. $ sudo nano /etc/sudoers.d/grader.
8. Fill that file with   grader ALL=(ALL:ALL) ALL, then save with CNTRL X then Y and then ENTER.
9. Next run update and upgrade pacakge commands, $ sudo apt-get update,  $ sudo apt-get upgrade.
10. Install finger $ sudo apt-get install finger (you can check the user we created by typing $ finger grader)
11. Open a new terminal and type $ ssh-keygen -f ~/.ssh/udacity_key.rsa
12. Create a passphrase for this key pair. (to opt out of creating a phrase just hit ENTER twice)
13. Once done in the same window where we created the key pair use cat to view the public key. $ cat ~/.ssh/udacity_key.rsa.pub. copy the information in the .pub file.
14. Head back to the original window where we are logged in as root, and move to the grader's folder. $ cd /home/grader.
15. Create a .ssh directory.
16. Create a file to store the piblic key. $ touch .ssh/authorized_keys
17. Now edit the file we created with $ sudo nano .ssh/authorized_keys and paste the information from udacity_key.rsa.pub.
18. change permissions. sudo chmod 700 /home/grader/.ssh and sudo chmod 644 /home/gader/.ssh
19. chang owner from root to grader $ sudo chown -R grader:grader /home/grader/.ssh
20. Restart the ssh service $ sudo service ssh restart
