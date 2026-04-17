**Access denied for user 'root'@'localhost' (using password: NO)**

1. Open and edit /etc/my.cnf or /etc/mysql/my.cnf, depending on your distribution.
2. Add skip-grant-tables under [mysqld]
3. Restart MySQL
4. You should be able to log in to MySQL now using the below command mysql -u root -p
5. Run mysql> flush privileges;
6. Set new password by *ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewPassword';*
7. Go back to /etc/my.cnf and **remove/comment** skip-grant-tables
8. Restart MySQL
9. Now you will be able to login with the new password mysql -u root -p
