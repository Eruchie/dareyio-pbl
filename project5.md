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