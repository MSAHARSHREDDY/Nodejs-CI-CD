step1-
	Make sure to upload your project in github
step2-
	Go to aws-console login page, After creating your account search for EC2 instance
step3-
	->Click on Launch Instance
	->Type the name of your Instance
	->Under Application and OS Images click on "Ubuntu"
	->Under Key pair login, create a new key pair that is going to download .pem file in your system
	->Now click on LaunchInstance,It is going to Launch your instance
	->Click on created instance,Once you click on that you find security, click on it, under you find security groups clik on that.
	->Click on Edit inbound rules
	->Click on Add Rule
	->Under dropdown select "HTTP" and another dropdown "anywhere IPV4" click save

Step4
	->Once you select the instance click on "connect"
	->Under "SSH Client" at bottom copy "ssh -i "Nodejs-CI-CD.pem" ubuntu@ec2-54-234-153-127.compute-1.amazonaws.com"

step5
	->In windows previsouly you have downloaded .pem file now open in windows poweshell
	->Paste this command "ssh -i "Nodejs-CI-CD.pem" ubuntu@ec2-54-234-153-127.compute-1.amazonaws.com" 
	->Now it is going to ubuntu terminal like this "ubuntu@ip-172-31-82-99:"

step6
	->Open github where your project has got uploaded
	->At Top you find setting option click on it
	->Under Actions click on Runners
	->Click on "new self hosted runner" and click on "linux"
	->Copy all the below commands in step by step till Download content"ubuntu@ip-172-31-82-99:"
	->Type "sudo ./svc.sh install" This tells we are action runner as backround service
	->Type "sudo ./svc.sh start"
	->Once yu click on runner before it is in "offline" now it is "idle"

step7
	->In github open setting click on "secret and variables" and click on "actions"
	->Enter the name as "PROD_ENV_FILE" and copy the .env files from you code and paste here
	->At top you find "actions" click on it
	->Under Continuous Integration click on Nodejs configure
	->Add some changes in nodejs.yaml file
		# This workflow will do a clean installation of node dependencies, cache/restore them, build the source code and run tests across different versions of node
		# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs

		name: Node.js CI/CD

		on:
		push:
			branches: [ "master" ]

		jobs:
		build:

			runs-on: self-hosted

			strategy:
			matrix:
				node-version: [20.x]
				# See supported Node.js release schedule at https://nodejs.org/en/about/releases/

			steps:
			- uses: actions/checkout@v4
			- name: Use Node.js ${{ matrix.node-version }}
			uses: actions/setup-node@v4
			with:
				node-version: ${{ matrix.node-version }}
				cache: 'npm'
			- run: npm ci
			- run: |
				touch .env
				echo "${{ secrets.PROD_ENV_FILE }}"> .env
			- run: pm2 restart BackendAPI #so what ever the changes we made in our code it is going to restart it aagain 


step 8
	->In ubuntu@ip-172-31-82-99:
	->Type "sudo apt update"
	->Type " curl -fsSL http://deb.nodesource.com/setup_lts.x | sudo -E bash -" for long supported versions
	->Type "sudo apt-get install -y nodejs"
	->Type "sudo apt-get install -y nginx"
	->Type "sudo npm i -g pm2" it is used for production
	->To confirm type "pm2"
	->Type " cd /etc/nginx/sites-available/"
	->Type "sudo nano default"
		
        location /api {
        rewrite ^\/api\/(.*)$ /api/$1 break;
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
	->Type "sudo systemctl restart nginx"
	->Type cd -
	->Now you go to the previous work directory of your code
	->Type "pm2 start server.js --name=BackendAPI"

