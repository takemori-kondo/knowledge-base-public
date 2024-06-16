# middleware-04. ASP.NET Core
________________________________________
Ubuntu での .NET の概要  
https://learn.microsoft.com/ja-jp/dotnet/core/install/linux-ubuntu

Ubuntu に .NET SDK または .NET ランタイムをインストールする  
https://learn.microsoft.com/ja-jp/dotnet/core/install/linux-ubuntu-install?pivots=os-linux-ubuntu-2004&tabs=dotnet8
________________________________________
## 0. How to Install & Run
________________________________________
Install & run sample (Ubuntu20, Console)

```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb

sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-8.0

sudo apt-get install -y dotnet-runtime-8.0

# .csprojのある場所
cd vagrant_shared/ConsoleApp/ConsoleApp

# configurationを省略すると(.csprojのデフォルトはDebugなので)Debugビルドされる
dotnet run --configuration Release
```

Install & run sample (CentOS7, Web App)

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