#Unraid 6.9.2 upgrade to 6.11 guide 
Unraid made some changes to the underlying docker version, which changed how dockers were built.
Dockers are no longer allowed to run as user ROOT. This is to mitigte security vulnerabilities if a 
docker is compromised (ala plex databases hacked again)


****PREPARATION****

<!-- Open terminal, run the command -->
ls -la /mnt/user/appdata/
Take note of all dockers that have root or a names user listed

For each docker that is running as root, edit the docker template and add the following as a label
Label and key should be the same, for simplicity

PGID 100
PUID 99
UMASK 000

Once this is done, the dockers changed might lose read right access to their appdat afolders. Run the following command for each affected dockers appdata folder

sudo chown -R nobody:users /mnt/user/appdata/*AFFECTEDDOCKERNAMEHERE*
<!-- Rinse and repeat for each docker that you added PUID,PGID and UMASK for  -->

<!-- Redis specific -->
Redis does not support the user and group of 99:100 UIDs 0-99 are reserved in linus for predefined user accounts, and redis wants a non user, system account with the proper permissions (root) so the PUID and PGID for other dockers wont work here. The group is set as root, with the -g 0 variable we set in the commands below.

<!-- for redis, open a terminal wondow, type the follwoing command  -->
sudo install -d -m 0755 -o 1001 -g 0 redis
<!-- once complete type the following command  -->
cd redis/
<!-- and finally  -->
sudo install -d -m 0755 -o 1001 -g 0 bitnami

ls -la /mnt/user/appdata/redis/ should now show nobody:users as the owner and group for redis. 