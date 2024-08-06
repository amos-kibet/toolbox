`Bug`: How to Fix Docker Error – “Got Permission Denied While Trying To Connect To The Docker Daemon Socket”?

Try this

Step 1: (replace USER with root username)

`sudo usermod -aG docker <USER>`

where USER is the username of your PC, for example, `kibet-pc`

Step 2:

`sudo setfacl --modify user:kibet-pc:rw /var/run/docker.sock`

Test run your docker command:

`socker run hello-world`
