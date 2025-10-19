# Fix issue wih LAMP Environment in Kubernetes
This is an extremely buggy lab. Was about to just skip it because I've spent one day already.

## Requirements
One of the DevOps team member was trying to install a WordPress website on a LAMP stack which is essentially deployed on Kubernetes cluster. 
It was working well and we could see the installation page a few hours ago. 
However something is messed up with the stack now due to a website went down. 
Please look into the issue and fix it:

- Deployment name is lamp-wp and its using a service named lamp-service
- The Apache is using http default port and nodeport is 30008.

From the application logs it has been identified that:
 - Application is facing some issues while connecting to the database in addition to other issues.
 - Additionally, there are some environment variables associated with the pods like:
     - MYSQL_ROOT_PASSWORD
     - MYSQL_DATABASE
     - MYSQL_USER
     - MYSQL_PASSWORD
     - MYSQL_HOST.
     - 
Also do not try to delete/modify any other existing components like deployment name, service name, types, labels etc.


## Steps

```bash
kubectl get pod,deploy,svc,secrets
```
2. From the httpd container, test connectivity to the MySQL.
```bash
  kubectl exec -it lamp-wp-56c7c454fc-cm9s7 -c httpd-php-container -- mysql
```
Result: error: Internal error occurred: error executing command in container: failed to exec in container: failed to start exec "3ec1ff6f26403ab5b3b4a7f94622dee230ac71fa6995c9283ebad334ad78e9aa": OCI runtime exec failed: exec failed: unable to start container process: exec: "mysql": executable file not found in $PATH: unknown

3. NOTE In some iterations of the labs, the nodePort could be showing 30009 or any other port instead of 30008. If you encountered this, retrieve the YAML and modify it.
```bash
kubectl get svc lamp-service -o yaml > lamp-svc.yml
vi lamp-svc.yml
```
While inside the vi editor, type this to replace all 30009 with 30008.
In addition to this, the port and targetPort might show as 8080. Change this to 80.

4. After changing, apply.
```bash
kubectl apply -f lamp-svc.yml 
service/lamp-service configured
```
5. Verify.
```bahs
kubectl get svc
```

6. To make the troubleshooting easier, we can save the container names and pod name.
```bash
HTTP=$(kubectl get pod  -o=jsonpath='{.items[*].spec.containers[0].name}') ; echo "HTTP: $HTTP"
```
HTTP: httpd-php-container
```bash
MYSQL=$(kubectl get pod -o=jsonpath='{.items[*].spec.containers[1].name}')  ; echo "MYSQL: $MYSQL"
```
MYSQL: mysql-container
```bash
POD=$(kubectl get pods -o=jsonpath='{.items[*].metadata.name}'); echo "POD: $POD"
```
POD: lamp-wp-56c7c454fc-s7xf5

7. Check the logs of the HTTP container. On all iterations of the labs, the logs will show that there was an incorrect variable. 
Now we'll immediately think that the environment variable needs to be corrected on the deployment YAML file, 
but this is not the case as the YAML file follows the instructions.

Instead, we need to modify the PHP file inside the HTTP container.
```bash
kubectl logs -f $POD -c $HTTP
```
Open a shell to the HTTP container and find the index.php file.
```bash
kubectl exec -it $POD -c $HTTP -- sh
/ # ls -la
/ # ls -la app
```
8. Check the index.app file. Here we can see that there's some typo error. Edit this to match the variables specified in the instructions/requirements.
```yaml
kubectl exec -it $POD -c $HTTP -- sh
/ # 
/ # cat /app/index.php 
<?php
$dbname = $_ENV['MYSQL_DATABASE'];
$dbuser = $_ENV['MYSQL_USER'];
$dbpass = $_ENV[''MYSQL_PASSWORD""];
$dbhost = $_ENV['MYSQL-HOST'];

$connect = mysqli_connect($dbhost, $dbuser, $dbpass) or die("Unable to Connect to '$dbhost'");

$test_query = "SHOW TABLES FROM $dbname";
$result = mysqli_query($test_query);

if ($result->connect_error) {
   die("Connection failed: " . $conn->connect_error);
}
  echo "Connected successfully";/
```
Specifically:
```yaml
$dbpass = $_ENV['MYSQL_PASSWORD'];   
$dbhost = $_ENV['MYSQL_HOST'];
```
9. While inside the container, restart the php-fpm service. Afterwards, exit out of the container.
```bash
/ # service php-fpm restart
php-fpm:php-fpmd: stopped
php-fpm:php-fpmd: started
/ # 
/ # service php-fpm status
php-fpm:php-fpmd                 RUNNING   pid 257, uptime 0:00:06 
/ # 
/ # exit
```
10. Checking the logs again:
```bash
kubectl logs -f $POD -c $HTTP
```

11. Check the environment variables in both containers.
```bash
kubectl exec -it $POD -c $HTTP -- env | grep -i mysql
```
```bash
kubectl exec -it $POD -c $MYSQL -- env | grep -i mysql
```
```bash
kubectl describe pod | grep -A 6 Env
```
   

