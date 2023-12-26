# vprofile-project-ci-jenkins
#step1: launch  instance
  ami: ubuntu
  type: t2.medium
  size: 10 gb
#step2: install java17 and jenkins
  create bash file
  sudo vi jenkins.sh(inside this file past java nd jenkins installion cmds)
      #!/bin/bash
      sudo apt update -y
      sudo su
      wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
      echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F=                 '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
      sudo apt update -y
      sudo apt install temurin-17-jdk -y
      /usr/bin/java --version
      curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
                      /usr/share/keyrings/jenkins-keyring.asc > /dev/null
      echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
                      https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
                                  /etc/apt/sources.list.d/jenkins.list > /dev/null
      sudo apt-get update -y
      sudo apt-get install jenkins -y
      sudo systemctl start jenkins
      sudo systemctl status jenkins
      (to save and exit this file Esc:wq!)
      once it has completed then full permissions to that file
      sudo chmod +x jenkins.sh
      ./jenkins.sh  (to run bash script)

      once it completed copy public ip and search in browser with extension number 8080
      
  
#step3: install nexus artifactory and configure it
      java -version
      cd /opt/
      sudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
      tar -zxvf latest-unix.tar.gz
      sudo mv /opt/nexus-3.30.1-01 /opt/nexus
      sudo adduser nexus
      sudo visudo (Add below line into it , save and exit)
      nexus ALL=(ALL) NOPASSWD: ALL
      sudo chown -R nexus:nexus /opt/nexus
      sudo chown -R nexus:nexus /opt/sonatype-work
      sudo nano /opt/nexus/bin/nexus.rc
      run_as_user="nexus" (to add nexus in "")

      sudo nano /etc/systemd/system/nexus.service  (To run nexus as service using Systemd and paste the below lines into it.)
      [Unit]
      Description=nexus service
      After=network.target

      [Service]
      Type=forking
      LimitNOFILE=65536
      ExecStart=/opt/nexus/bin/nexus start
      ExecStop=/opt/nexus/bin/nexus stop
      User=nexus
      Restart=on-abort

      [Install]
      WantedBy=multi-user.target

      sudo systemctl start nexus (To start nexus service using systemctl)
      sudo systemctl enable nexus (To enable nexus service at system startup)
      sudo systemctl status nexus (To check nexus service status)

      Access Nexus Repository Web Interface
      http://server_IP:8081
      To login to Nexus, click on Sign In, default username is admin
      cat /opt/nexus/sonatype-work/nexus3/admin.password (To find default password run the below command)
      once completed 
      
      
#step4: create vprofile job with declarative pipeline
    goto jenkins server goto managejenkins=> plugins=>install maven and nexusartifactory & eclips tumarin pulgins
    then => manage jenkins=>credentials=>usernamewithpasswd =>usernamr:<provide nexus repository name >passwd:<provide repo      passwd> =>name:<nexuscredentials> => save and apply
    then goto managejenkins=> toos=> add jdk name:jdk17 install from <adoptium.net>
    select java17+9+0 version => and add maven name:mvn3 =>apply and save
    goto dash board=> newitem =>v-profile
    goto pipeline past the pipeline build now 
    
