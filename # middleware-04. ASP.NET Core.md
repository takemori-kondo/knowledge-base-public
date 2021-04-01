# middleware-04. ASP.NET Core
________________________________________
## 0. How to Install
________________________________________
Install

```bash
sudo yum -y install httpd
sudo vi /etc/httpd/conf/httpd.conf
#<VirtualHost *:80>
#  ProxyPreserveHost On
#  ProxyPass / http://127.0.0.1:5000/
#  ProxyPassReverse / http://127.0.0.1:5000/
#</VirtualHost>
sudo systemctl enable httpd.service
sudo systemctl restart httpd.service

sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
sudo yum -y update
sudo yum -y install dotnet-sdk-2.1
# create Core2.1 Web MVC project without https or comment out it.
#     Startup.cs
#     ...
#     // app.UseHsts();
#     ...
#     // app.UseHttpsRedirection();
# copy to /home/vagrant/vagrant_shared/ (This is guest additions shared directry)
cd vagrant_shared/WebApplication1/WebApplication1/
dotnet restore
dotnet build
dotnet run
```