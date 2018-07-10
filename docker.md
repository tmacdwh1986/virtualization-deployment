sudo groupadd docker
sudo gpasswd -a $USER docker

sudo service docker restart
newgrp - docker
