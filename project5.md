**IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)**
___
1. This Project is required to demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS), follow the below instructions. We will have to create and configure two Linux-based virtual servers; `mysql server` and `mysql client`.

1. On `mysql server` install `mysql-server` software.

   - `sudo apt install mysql-server`

      ![step1](./project5Pictures/step1_p5.JPG)

    - `sudo apt systemctl status mysql`

      ![step2](./project5Pictures/step2_p5.JPG)

1. Log into mysql console.

   - `sudo mysql`

      ![step3](./project5Pictures/step3_p5.JPG)

1. Run a script to remove some insecure default settings. Set user’s password as PassWord.1

    - `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

      ![step4](./project5Pictures/step4_p5.JPG)

1. Start the interactive script by running the command below:

    - `sudo mysql_secure_installation`

      ![step5](./project5Pictures/step5_p5.JPG)

1. Test login to mysql-server using the command below:

    - `sudo mysql -p`

      ![step6](./project5Pictures/step6_p5.JPG)


1. Allow access/connections to `mysql client`from remote hosts and repace `127.0.0.1` with `0.0.0.0`

    - `sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

      ![step7](./project5Pictures/step7_p5.JPG)

1. Create a user in `mysql` and add the IP address of the client system you want to grant access to and then give privileges. In our case the client IP is `3.22.100.101`.

    - `sudo mysql -p`
    - `CREATE USER 'tarzan'@'3.22.100.101' IDENTIFIED BY 'P@ssw0rd';`
    - `GRANT ALL PRIVILEGES ON *.* TO 'tarzan'@'3.22.100.101';`
    
      ![step8](./project5Pictures/step8_p5.JPG)

1. Navigate to `mysql-client` and connect remotely to `mysql-server` database engine with the user name `tarzan` and `mysql-server` IP; `18.116.239.78` in our own case. Also check the databases once connected sussessfully.

    - `mysql -u tarzan -h 18.116.239.78 -p`
    - `SHOW DATABASES;`
    
      ![step9](./project5Pictures/step9_p5.JPG)

1. Create a database `auforaDB` and then `SHOW DATABASES;` to see if it reflects.

    - `CREATE DATABASE auforaDB;`
    - `SHOW DATABASES;`
    
      ![step10](./project5Pictures/step10_p5.JPG)

1. Remove the database `auforaDB` and then `SHOW DATABASES;` to confirm it.

    - `DROP DATABASE auforaDB;`
    - `SHOW DATABASES;`
    
      ![step11](./project5Pictures/step11_p5.JPG)      