# [OPTIONAL] Connecting external clients to MDS

For testing purposes, you can provision a public Load Balancer to listen on default port 3306 to connect your on-premises or cloud applications to MDS

1. Create a Load Balancer
![lb-1](img/lb-1.jpg)
2. Use the default settings
![lb-2](img/lb-2.jpg)
3. Choose the right VCN
![lb-3](img/lb-3.jpg)
4. Specify the TCP protocol and port 3306
![lb-4](img/lb-4.jpg)
5. Select TCP protocol and port 3306 for the **listener**
![lb-5](img/lb-5.jpg)
6. Create the load balancer
![lb-6](img/lb-6.jpg)


