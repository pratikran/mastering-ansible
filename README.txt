Configuration Management and Orchestration
    Abstraction of activities
    Idempotent actions
    
Preparation
  Environment Setup
    https://www.vagrantup.com/
    Topology.pdf
    Vagrantfile
    
  Installation
    http://docs.ansible.com/ansible/latest/intro_installation.html
    
    Ansible is python aplication
        can be install using pip
        
    Better is Ubuntu Apt install
        http://docs.ansible.com/ansible/latest/intro_installation.html#latest-releases-via-apt-ubuntu
        
        $ sudo apt-get update
        $ sudo apt-get install software-properties-common
        $ sudo apt-add-repository ppa:ansible/ansible
        $ sudo apt-get update
        $ sudo apt-get install ansible
        
        On older Ubuntu distributions, “software-properties-common” is called “python-software-properties”.
        
    check
        ansible --version
                ansible 2.3.2.0 (tutors version was 1.9.3)
                python 2.7.6
        ansible-playbook --version
                ansible-playbook 2.3.2.0
                python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
        ansible-galaxy --version
                ansible-galaxy 2.3.2.0
                python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
            
        config file = /etc/ansible/ansible.cfg
        
FOUNDATION
    
  INVENTORY Pt1
    http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html
    http://docs.ansible.com/ansible/latest/intro_inventory.html
    
    Populate Target environment
    Inventory
        Static
        Dynamic
    
        Default Inventory
                ansible --list-hosts all
          file
                vim /etc/ansible/hosts
                        Delete pre-defined hosts
                            may interfere with our inventory
        ansible --list-hosts all
                0 hosts
    
   INVENTORY Pt2
     
     F:\Code\devops\udemy-mastering-ansible\mastering-ansible\S2
            Vagrantfile
            mkdir -p ansible/dev
            cd ansible/dev
     In Editor
        create a Inventory File dev-1
            (It can be simple group of host names)
            (It can be groups of different type of servers)
            
       vagrant ssh control
       cd /vagrant/ansible/dev
       ansible --list-hosts all
            0 hosts
       ansible -i dev-1 --list-hosts all
            5 hosts
            
     By default, ansible ssh into hosts
        even on control/workstation host
            totally unnecessary as we work from control host
                so add in dev-1 file,
                        control ansible_connection=local
       
     Order of Inventory file is set in 
            /etc/ansible/ansible.cfg
                inventory = file
            override to local dev/other file
                
                cd /vagrant/ansible/dev
                
                create local ansible.cfg
                    inventory = ./dev-1
        
                ansible --list-hosts all
                        5 hosts
                        
   HOST SELECTION
       Ansible Docs - Patterns
            http://docs.ansible.com/ansible/latest/intro_patterns.html
            
       Commands can be run against a subset of inventory
         using patterns
        
       COMMON PATTERNS
            ansible --list-hosts all
            ansible --list-hosts "*"
            
            ansible --list-hosts loadbalancer
            ansible --list-hosts webserver
            
            ansible --list-hosts db01
            
            ansible --list-hosts "app0*"
            
            ansible --list-hosts database:control
                    in future ":" can be replaced with ","
                    
            ansible --list-hosts webserver[0]
                    used index 
                    
            ansible --list-hosts \!control
                    negation
                    
            
            
            
       
       
                
                
    
  
    
    
    
