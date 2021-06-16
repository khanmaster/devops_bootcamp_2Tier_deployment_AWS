

```bash
ssh -o "StrictHostKeyChecking=no" ubuntu@ip <<EOF
	pkill -f tmux
EOF
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@ip:~/project
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@ip:~/project
ssh -o "StrictHostKeyChecking=no" ubuntu@ip <<EOF
	cd project/environment/app
    chmod +rwx provision.sh
    sudo ./provision.sh
    cd ~/project/app
    npm install
    tmux new-session -d -s app
    tmux send-keys -t app 'nodejs app.js' C-m
EOF
```

## Automated deployment
```bash
rm -rf eng84_devops_CICD*
git clone -b main https://github.com/JClarke-96/eng84_devops_CICD.git
cd eng84_devops_CICD

rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@34.245.43.224:/home/ubuntu
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@34.245.43.224:/home/ubuntu

ssh -A -o "StrictHostKeyChecking=no" ubuntu@34.245.43.224 <<EOF
	# 'kill' all running instances of node.js
	killall npm
    
	# run provisions file for dependencies
	cd /home/ubuntu/environment/app
	chmod +x provision.sh
	./provision.sh
    
	# Install npm for remaining dependencies
	cd /home/ubuntu/app
	sudo npm install
	node seeds/seed.js

EOF
```
```bash
# synchronisining folders app from repo and app to instance
rsync -avz -e "ssh -o StrictHostKeyChecking=no" cicd/app ubuntu@34.245.213.38:~/
rsync -avz -e "ssh -o StrictHostKeyChecking=no" cicd/environment ubuntu@34.245.213.38:~/
ssh -o "StrictHostKeyChecking=no" ubuntu@34.245.213.38 <<EOF
	sudo bash ./environment/provision.sh
    
    cd app
    sudo npm install
EOF
```

```bash
#rm -rf eng84_CI_CD_Jenkins*
#git clone -b main https://github.com/alfonso-torres/eng84_CI_CD_Jenkins.git
#cd eng84_CI_CD_Jenkins
echo TEST-----------
ls -la
echo --------TEST
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@3.250.167.100:/home/ubuntu/app
#rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@34.253.229.149:/home/app

ssh -A -o "StrictHostKeyChecking=no" ubuntu@3.250.167.100<<EOF
	
    killall npm
    
    #sudo bash ./environment/provision.sh
    
    cd app/app
    ls -la
    sudo npm install
    nodejs seeds/seed.js
    
    nodejs app.js &
    
EOF
```
```bash
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@54.246.133.168:/home/ubuntu/
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@54.246.133.168:/home/ubuntu/

ssh -o "StrictHostKeyChecking=no" ubuntu@54.246.133.168 <<EOF

	cd ./environment/app/
    sudo chmod +x ./provision.sh
    IS_NGINX=$(ps -aux | grep nginx | wc -l)
    if [ ${IS_NGINX} -ne 0 ]
    then
        sudo ./provision.sh
    fi
    cd ..
    cd ..
    cd app
    sudo npm install
    pkill -f tmux
    sudo apt-get install tmux -y
    tmux new-session -d -s app
    tmux send-keys -t app 'node app.js' C-m

EOF
```

```bash

ssh -A -o "StrictHostKeyChecking=no" ubuntu@54.229.213.187<<EOF
	
    killall npm
    rm -rvf application*

    
    git clone -b main https://github.com/conjectures/eng84_cicd_jenkins_task.git
    mv eng84_cicd_jenkins_task/application .
    rm -rvf eng84_cicd_jenkins_task*
    
    sudo npm install application/app
    nodejs $HOME/application/app/seed/seed.js
    
    npm start --prefix $HOME/application/app/ &
    
    
EOF

```

```bash
rm -rf eng84_cicd_jenkins*
git clone -b main https://github.com/isobelfc/eng84_cicd_jenkins.git
cd eng84_cicd_jenkins
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@34.243.129.193:/home/ubuntu/app
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@34.243.129.193:/home/ubuntu/app

ssh -o "StrictHostKeyChecking=no" ubuntu@34.243.129.193 <<EOF

	killall npm

	cd app/app
    sudo npm install
    nodejs seeds/seed.js
    
    nodejs app.js &

EOF
```

```bash
rm -rf eng84_cicd_jenkins*
git clone -b main https://github.com/William-King977/eng84_cicd_jenkins.git
cd eng84_cicd_jenkins

rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@63.34.10.59:/home/ubuntu/app
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@63.34.10.59:/home/ubuntu/app

ssh -A -o "StrictHostKeyChecking=no" ubuntu@63.34.10.59 <<EOF
    # 'kill' all running instances of node.js
    killall npm
    
    # run provisions file for dependencies
    cd /home/ubuntu/app/environment/app
    chmod +x provision.sh
    ./provision.sh
    
    # Install npm for remaining dependencies
    cd /home/ubuntu/app/app
    sudo npm install
    node seeds/seed.js
     
    # Run the app
    node app.js &

EOF
```